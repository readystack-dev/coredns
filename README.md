# CoreDNS: ReadyStack build kit

The offline build kit for the Docker image `readystack/coredns:v1.14.2-CE-ubuntu24.04-r4`. It holds what the image is built from (upstream source, base images, toolchain, pinned dependencies) and the documents, so the image can be rebuilt with no network access (`docker build --network=none`).

- Release: [v1.14.2-CE-ubuntu24.04-r4](https://github.com/readystack-dev/coredns/releases/tag/v1.14.2-CE-ubuntu24.04-r4)
- Image: [`docker.io/readystack/coredns:v1.14.2-CE-ubuntu24.04-r4`](https://hub.docker.com/r/readystack/coredns/tags), manifest digest `sha256:048cd4a243b437f57cf63dceaaace4d4a456b32f8c8188952f298e8055e2b9eb`
- Product page: https://readystack.dev/products/coredns
- Defects: this repository's Issues (defects recorded from 25 November 2026 on) and https://readystack.dev/defects.html?product=coredns

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
