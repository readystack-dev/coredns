# =====================================================================
# CoreDNS v1.14.2 L-009 HERMETIC OFFLINE BUILD: the canonical, attestation-producing build. build_kit runs
#   docker buildx build --builder readystack-builder-max --network=none
#     --attest type=provenance,mode=max,version=v1 --attest type=sbom --push <directory context>
# Pinned-public-base + vendored model: golang 1.26.10 and ubuntu 24.04 pinned BY DIGEST (BASE_IMAGE_DIGESTS.lock);
# vendored/coredns-src is the fully vendored Go tree (GOPROXY=off, -mod=vendor); vendored/chisel the chiselled Ubuntu
# 24.04 runtime (gen_dockerfile.py's runtime_base cut); vendored/debs libcap2-bin for setcap in the rootfs stage only.
# Dockerfile and Dockerfile.offline are byte copies of this file (the repo-wide convention).
#
# SHAPE: upstream's image (coredns/coredns Dockerfile at v1.14.2: distroless static, /coredns with
# `setcap cap_net_bind_service=+ep`, USER nonroot:nonroot = 65532, WORKDIR /, ENTRYPOINT ["/coredns"]) on a chiselled Ubuntu 24.04
# runtime instead of distroless. The file capability lets the non-root user bind :53 under kubeadm v1.36.6's CoreDNS
# Deployment (capabilities add NET_BIND_SERVICE, drop ALL; allowPrivilegeEscalation false; readOnlyRootFilesystem):
# with no_new_privs a file capability is still honoured when the process already holds it in its permitted set, which
# the runtime grants from the Pod's add list.
# =====================================================================

# --- CoreDNS v1.14.2 (Makefile: CGO off, coremain.GitCommit stamp; plugin.cfg's generated zplugin.go/zdirectives.go are
#     committed at the tag) -------------------------------------------------------------------------------------------
FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_coredns
WORKDIR /coredns
COPY vendored/coredns-src/ .
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOWORK=off GOFLAGS="-mod=vendor -buildvcs=false" GOPROXY=off
RUN mkdir -p /out && go build -trimpath -ldflags "-s -w -X github.com/coredns/coredns/coremain.GitCommit=dd1df4f" -o /out/coredns . && \
    /out/coredns -version | grep -q '^CoreDNS-1.14.2$' && ! readelf -l /out/coredns | grep -q INTERP && \
    v=$(go version /out/coredns | awk '{print $2}') && \
    { [ "$(printf '%s\n%s\n' go1.26.11 "$v" | sort -V | head -n1)" = go1.26.11 ] || \
      { echo "coredns was built with '$v', older than the go1.26.11 floor" >&2; exit 1; }; }

# --- rs-probe: the HEALTHCHECK helper of the fleet's slim images (standard library only; rs-probe.go is
#     gen_dockerfile.py's RS_PROBE_GO v2, verbatim) -----------------------------------------------------------------------
FROM golang:1.26.11-bookworm@sha256:dc40a13b61304647ed7e5d581d246b86413dfc29d63c6d018310feb93cd98fc0 AS build_rs_probe
WORKDIR /probe
COPY rs-probe.go main.go
COPY rs-probe.mod go.mod
ENV CGO_ENABLED=0 GOTOOLCHAIN=local GOFLAGS="-buildvcs=false" GOPROXY=off
RUN go build -trimpath -ldflags='-s -w' -o /rs-probe .

# --- the chiselled rootfs, the nonroot user (distroless's: uid/gid 65532), /coredns with its file capability ---------
FROM ubuntu:24.04@sha256:9c7f200c07e3d4a03e7249d2c16bf66a51120fb876aa2242cf31451412543289 AS rootfs
ARG SOURCE_DATE_EPOCH=1795404131
# setcap (libcap2-bin; libcap2 is in the pinned base) for this stage only: vendored/debs is bind-mounted, never a layer
RUN --mount=type=bind,source=vendored/debs,target=/tmp/debs dpkg -i /tmp/debs/libcap2-bin_*.deb >/dev/null && \
    command -v setcap >/dev/null
ADD vendored/chisel/rootfs.tar /rootfs/
RUN printf 'nonroot:x:65532:65532:nonroot:/home/nonroot:/sbin/nologin\n' >> /rootfs/etc/passwd && \
    printf 'nonroot:x:65532:\n' >> /rootfs/etc/group && \
    mkdir -p /rootfs/home/nonroot /rootfs/etc/coredns && chown 65532:65532 /rootfs/home/nonroot && chmod 0700 /rootfs/home/nonroot
COPY --from=build_coredns /out/coredns /rootfs/coredns
COPY --from=build_rs_probe /rs-probe /rootfs/usr/local/bin/rs-probe
COPY Corefile /rootfs/etc/coredns/Corefile
RUN --mount=type=bind,source=vendored/chisel,target=/chisel set -eu; \
    chmod 0755 /rootfs/coredns /rootfs/usr/local/bin/rs-probe && chmod 0644 /rootfs/etc/coredns/Corefile && \
    setcap cap_net_bind_service=+ep /rootfs/coredns && \
    [ "$(getcap /rootfs/coredns)" = "/rootfs/coredns cap_net_bind_service=ep" ] || \
      { echo "/coredns does not carry cap_net_bind_service=ep: $(getcap /rootfs/coredns)" >&2; exit 1; }; \
    [ -e /rootfs/etc/os-release ] || { echo "no /etc/os-release: base-files_release-info is missing" >&2; exit 1; }; \
    [ "$(grep -c '^Package: ' /rootfs/var/lib/dpkg/status)" = "$(wc -l < /chisel/packages.tsv)" ] || \
      { echo "/var/lib/dpkg/status does not list every package of vendored/chisel/packages.tsv" >&2; exit 1; }; \
    rm -rf /rootfs/var/log/dpkg.log /rootfs/var/log/alternatives.log /rootfs/var/log/apt /rootfs/var/cache/ldconfig/aux-cache /rootfs/var/cache/fontconfig

FROM scratch
ARG SOURCE_DATE_EPOCH=1795404131
COPY --from=rootfs /rootfs/ /
ENV PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HEALTHCHECK --interval=10s --timeout=5s --start-period=10s \
  CMD ["/usr/local/bin/rs-probe", "http://127.0.0.1:8080/health"]
USER 65532:65532
WORKDIR /
EXPOSE 53 53/udp
ENTRYPOINT ["/coredns"]
CMD ["-conf", "/etc/coredns/Corefile"]
# >>> readystack OCI labels: strategies/coredns.json image_labels plus the standard OCI labels,
# the set a generated kit carries (gen_dockerfile.label_lines). Written by scripts/kit_migrations.py;
# scripts/retarget.py re-renders it with every retarget. Never edit it by hand.
LABEL org.opencontainers.image.title="CoreDNS"
LABEL org.opencontainers.image.version="v1.14.2-CE-ubuntu24.04-r4"
LABEL org.opencontainers.image.licenses="Apache-2.0"
LABEL org.opencontainers.image.source="https://github.com/coredns/coredns"
LABEL org.opencontainers.image.vendor="ReadyStack.dev"
LABEL org.opencontainers.image.revision="dd1df4f5db93767a44e37638df44969503b320d2"
LABEL org.opencontainers.image.description="CoreDNS v1.14.2 built from source: the DNS server Kubernetes uses for cluster DNS, non-root (uid 65532) and able to bind port 53 through a file capability, on a chiselled Ubuntu 24.04 runtime."
# <<< readystack OCI labels
