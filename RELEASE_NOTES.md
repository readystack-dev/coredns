# CoreDNS v1.14.2 — ReadyStack release `v1.14.2-CE-ubuntu24.04-r4`

Generated 2026-11-23 from this release's build records and strategy. Statements that come from a single record name it in brackets; the others come from this release's strategy and build log.

## Release

- **Image:** `readystack/coredns:v1.14.2-CE-ubuntu24.04-r4` (linux/amd64). Index digest `sha256:7064106dc719e98f2c77765073f39166f72de82be2b800e7c48bcb0523b37e46` [`docs/provenance/LOCKED_DIGEST.txt`].
- **Image manifest digest:** `sha256:048cd4a243b437f57cf63dceaaace4d4a456b32f8c8188952f298e8055e2b9eb` [`TARGET_DIGEST.txt`].
- **Upstream:** CoreDNS v1.14.2 built from source at `v1.14.2` of https://github.com/coredns/coredns — upstream notes: https://github.com/coredns/coredns/releases/tag/v1.14.2 [`docs/provenance/BUILD_RECIPE.json`].
- **Base image:** `ubuntu:24.04`; runtime user `65532`.
- **Build:** `source-rebuild-go`, compiled offline with `--network=none`.
- **Supersedes:** ReadyStack release `v1.14.2-CE-ubuntu24.04-r3`.

## What changed in this release

- Upstream version unchanged (v1.14.2); ReadyStack rebuild.
- Minimum dependency versions ReadyStack enforces on top of upstream (raised only where upstream's own version was lower); shipped versions from the SBOM [`docs/sbom/SBOM.json`]:
  - `golang.org/x/net` raised from upstream's `v0.51.0` to at least `v0.60.0` (shipped: `v0.62.0`)
  - `golang.org/x/crypto` raised from upstream's `v0.48.0` to at least `v0.56.0` (shipped: `v0.58.0`)
  - `golang.org/x/text` raised from upstream's `v0.34.0` to at least `v0.41.0` (shipped: `v0.43.0`)
  - `google.golang.org/grpc` raised from upstream's `v1.79.1` to at least `v1.83.2` (shipped: `v1.83.2`)
  - `github.com/klauspost/compress` raised from upstream's `v1.18.0` to at least `v1.18.7` (shipped: `v1.20.2`)
  - `github.com/DataDog/dd-trace-go/v2` raised from upstream's `v2.6.0` to at least `v2.8.2` (shipped: `v2.8.2`)
  - `go.etcd.io/etcd/client/pkg/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
  - `go.etcd.io/etcd/api/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
  - `go.etcd.io/etcd/client/v3` raised from upstream's `v3.6.8` to at least `v3.6.16` (shipped: `v3.6.16`)
- CoreDNS v1.14.2 is unchanged from the release this supersedes (v1.14.2-CE-ubuntu24.04-r3); this release rebuilds it. Changes: libc6 moves from 2.39-0ubuntu8.9 to 2.39-0ubuntu8.12 (CVE-2026-18374, CVE-2026-84243, CVE-2026-8674, CVE-2026-86805, ...). Settings, ports and data volumes are those of v1.14.2-CE-ubuntu24.04-r3: replace the container with this image on the same volumes.

## Security status at release (measured 2026-11-23)

- **Docker Scout:** 0 fixable critical/high vulnerabilities; runs as `65532:65532` (non-root); SBOM + provenance attestations present (SPDX SBOM + SLSA v1) [`docs/provenance/SCOUT_VERDICT.json`].
- **OSV cross-check:** no fixable critical/high vulnerability beyond Docker Scout's findings.
- **Signature:** cosign signature verified by ReadyStack's release gate before publishing; check it yourself with `cosign verify --key https://readystack.dev/keys/cosign.pub readystack/coredns:v1.14.2-CE-ubuntu24.04-r4` (signatures are recorded in the public Sigstore transparency log).
- **ReadyStack release check at release:** passed (ReadyStack's own test, not a Docker Scout grade). Vulnerabilities are disclosed every day and the image never changes, so it is re-checked daily, beside Docker Scout's own dated grade, at https://readystack.dev/queue.
- **Before exposing this service:** The health (8080) and ready (8181) endpoints answer without authentication; publish only port 53 unless something outside the host needs them.
- **Before exposing this service:** An open resolver: the image's default Corefile answers any client. Restrict who can reach port 53, or use your own Corefile with the acl plugin.

## Reproducibility

Rebuilds offline from this archive to the identical image digest `sha256:048cd4a243b437f57cf63dceaaace4d4a456b32f8c8188952f298e8055e2b9eb`: run `./BUILD_OFFLINE.sh`, then `./VERIFY_OFFLINE.sh` (add `--online` to confirm the Docker Hub tag holds the same digest). Before publishing, ReadyStack rebuilt the kit with `./BUILD_OFFLINE.sh` and compared the result with the published digest [`docs/provenance/REPRO_VERIFICATION.json`], then ran `./BUILD_OFFLINE.sh` and `./VERIFY_OFFLINE.sh` from this archive (without `--online`).

## Validation

- 3 of 3 use cases passed (3 authored) against the running image [`docs/validation/test-results.json`].
- The cases are `/usr/local/bin/rs-probe -body http://127.0.0.1:8181/ready` (exec-rc0), `/coredns -version` (exec-rc0), `/usr/local/bin/rs-probe -body http://127.0.0.1:8080/health` (exec-rc0); the suite checks nothing else.

## Known issues

- None found (reviewed by held semantic review (Claude) on 2026-11-23).

## Verify this archive

```
sha256sum -c CHECKSUMS.sha256
./BUILD_OFFLINE.sh && ./VERIFY_OFFLINE.sh --online
cosign verify --key https://readystack.dev/keys/cosign.pub readystack/coredns:v1.14.2-CE-ubuntu24.04-r4
```

ReadyStack records every signature in the public Sigstore transparency log (Rekor), so the command needs no extra flags.

