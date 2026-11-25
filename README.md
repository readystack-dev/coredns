# CoreDNS: ReadyStack build kit

The offline build kit for the Docker image `readystack/coredns:v1.14.2-CE-ubuntu24.04-r4`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v1.14.2-CE-ubuntu24.04-r4](https://github.com/readystack-dev/coredns/releases/tag/v1.14.2-CE-ubuntu24.04-r4)
- Image: [`docker.io/readystack/coredns:v1.14.2-CE-ubuntu24.04-r4`](https://hub.docker.com/r/readystack/coredns/tags), manifest digest `sha256:048cd4a243b437f57cf63dceaaace4d4a456b32f8c8188952f298e8055e2b9eb`
- Product page: https://readystack.dev/products/coredns
- Source and build recipe: https://readystack.dev/products/coredns/recipe
- Build trail: https://readystack.dev/products/coredns/trail/v1.14.2-CE-ubuntu24.04-r4
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=coredns

## The image

A single container running the CoreDNS DNS server as the non-root user 65532 (nonroot). /coredns carries the file capability cap_net_bind_service, so the non-root process can bind port 53 without root. The default command is `-conf /etc/coredns/Corefile`: the image's Corefile answers every query with CoreDNS's whoami plugin (upstream's own default) and serves /health on 8080 and /ready on 8181. Mount your own Corefile at /etc/coredns/Corefile, as Kubernetes does with its coredns ConfigMap. Arguments after the image name replace the default command, so repeat `-conf /etc/coredns/Corefile` before any extra flag.

## Who uses CoreDNS and why

Platform engineers and network administrators who run DNS for Kubernetes clusters or internal networks.

CoreDNS is a DNS server built from plugins. It can serve zone files, forward and cache queries, and answer for Kubernetes services, each feature switched on in its configuration file.

A typical use: In a Kubernetes cluster, CoreDNS answers pods' lookups for the cluster's service names and forwards other names to upstream resolvers. On an office network, the same server can serve internal names from a zone file and cache lookups for everything else.

From the upstream project's own description: https://github.com/coredns/coredns, https://coredns.io/manual/toc/.

## This release

- Docker Scout policy grade at release: **A** (94%), graded 2026-11-23 with `docker scout policy` (CLI 1.26.0, docker-default configuration).
  - Docker Scout counts 7 packages with copyleft licences (GPL, LGPL, MPL), all of them operating-system packages (it counts only packages whose licence it can read; the SBOM lists every component).
- Signed with ReadyStack's release key (fingerprint `04daa92344a52dbc`): https://readystack.dev/keys/cosign.pub.
- Runs as a non-root user (`65532:65532`).
- Attestations on the image: an SPDX SBOM and SLSA provenance v1.
- Rebuilt from this kit with network egress blocked on 2026-11-23; the rebuilt image digest is the published one.
- Base: Ubuntu 24.04.

The current grade, measured again every day, is on https://readystack.dev/products/coredns.

CoreDNS is distributed under its upstream licence (Apache-2.0); the kit contains upstream's source under that licence, and each vendored component under its own.

## Download

From the [v1.14.2-CE-ubuntu24.04-r4 release](https://github.com/readystack-dev/coredns/releases/tag/v1.14.2-CE-ubuntu24.04-r4): `ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.tar.gz` (444.7 MB), SHA-256 `5d6b93f387972be373ada46160ff6e3b9e914986079bd89992137616877b91a9`.

```sh
sha256sum --check --ignore-missing SHA256SUMS
```

## Verify and rebuild

The rebuild statement is signed with the ReadyStack release key. Check the signature before you rely on it:

```sh
cosign verify-blob --key https://readystack.dev/keys/cosign.pub \
  --bundle ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.rebuild.json.sigstore ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.rebuild.json
```

Unpack with `-p` so file modes are kept, then follow the kit's own README.md and docs/REBUILD.md:

```sh
tar -xpzf ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.tar.gz
cd v1.14.2-CE-ubuntu24.04-r4
less README.md docs/REBUILD.md
```

The build trail (`ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.trail.json`, signed the same way, and its Markdown copy) records how this release was built and checked.

## This repository

`recipe/` holds the kit's recipe files (Dockerfiles, build scripts, pins) as published at https://readystack.dev/products/coredns/recipe. The complete kit is the release download. Each release is a tag here.

— ReadyStack · https://readystack.dev
