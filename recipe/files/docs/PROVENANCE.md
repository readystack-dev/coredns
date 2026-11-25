# Provenance — CoreDNS (`v1.14.2-CE-ubuntu24.04-r4`)

Every item below is copied from a record in this archive; the record is named in brackets.

## Source

- Upstream repository: https://github.com/coredns/coredns
- Revision built: `v1.14.2` (commit `dd1df4f5db93767a44e37638df44969503b320d2`)
- Source tree: `f69cbed3da6f6a1dbbddc86418a6aae8588c6ecf4c604208b9eba41225f42670` over 917 files [`docs/provenance/SOURCE_TREE_HASH.json`]
- Community Edition only: enterprise code is removed or refused before the build (CE audit at acquire).

## Build

- Method: `source-rebuild-go`; base image `ubuntu:24.04`
- Compiled with `--network=none` from vendored source, dependencies and base images; BuildKit pinned to `moby/buildkit:v0.31.2` (shipped in `toolchain/`).
- SOURCE_DATE_EPOCH: `1795404131` [`SOURCE_DATE_EPOCH.txt`]
- Frozen inputs: [`docs/provenance/FREEZE_MANIFEST.json`], build recipe: [`docs/provenance/BUILD_RECIPE.json`].

## Output

- Published image: `readystack/coredns:v1.14.2-CE-ubuntu24.04-r4` — index digest `sha256:7064106dc719e98f2c77765073f39166f72de82be2b800e7c48bcb0523b37e46` [`docs/provenance/LOCKED_DIGEST.txt`]
- linux/amd64 image digest `sha256:048cd4a243b437f57cf63dceaaace4d4a456b32f8c8188952f298e8055e2b9eb` — reproduced from this archive before publishing [`docs/provenance/REPRO_VERIFICATION.json`].

## Attestations and signature

- SBOM + SLSA provenance attached to the image: attached: an SPDX SBOM and SLSA v1 provenance [`docs/provenance/SCOUT_VERDICT.json`]
- cosign signature by the ReadyStack release key: verified before publishing. Check it: `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/coredns:v1.14.2-CE-ubuntu24.04-r4`

## Pinned inputs, and why

- 35 pinned inputs, each with where it came from, why this one, how it was checked and when it was decided: `docs/PINS.md` [`docs/provenance/PINS.json`] (upstream source 2, base and build images 3, image digests 2, dependency versions readystack sets 10, pins in the kit's dockerfiles 15, readystack's build engine 1, dependencies upstream chose 2).

## Build trail

- How this release passed through the ReadyStack harness, record by record (every chain, drive, stage, review round, defect and check, each naming the record it is copied from): `ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.trail.json`, published beside this archive and signed with the release key (`ReadyStack-coredns-v1.14.2-CE-ubuntu24.04-r4.trail.json.sigstore`), and readable at https://readystack.dev/products/coredns/trail/v1.14.2-CE-ubuntu24.04-r4.

