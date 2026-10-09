# SHM-SEC-001: forwarding-header rate-limit bypass

## Record provenance

This record republishes the existing SHM security finding and its already implemented correction. Repository republication does not represent a new discovery or a newly developed fix. No prior PR, advisory identifier or Git commit is represented as a record created in this repository.

The `main` branch contains the remediated application. The separate `regression-baseline` branch is an imported historical backend snapshot for isolated regression testing; it is not a deployable release. Both branches have newly created Git identities. Its `SNAPSHOT.json` records the SHA256 of every imported backend file and the aggregate source manifest digest. The security tool's `UPSTREAM.lock` identifies the new fixed and historical snapshot commits used by the tests.

## Finding and impact

The historical `ApiRateLimitFilter` selected a caller-supplied `X-Forwarded-For`, or `X-Real-IP`, before the socket peer, without verifying a trusted proxy. A reachable caller could obtain different rate-limit buckets by changing that header. Authorized synthetic tests established a per-client rate-limit bypass. They did not establish an outage, authentication bypass, privilege escalation or code execution.

The documented demo uses loopback and has no established authentication boundary. Exposure depends on deployment reachability and proxy header handling. The maintainer classifies the issue as **CWE-807**, with **Low** severity for the demonstrated scope; no CVE is claimed.

## Existing correction

The fixed implementation uses the original connection peer, ignores `X-Forwarded-For`, and accepts exactly one valid `X-Real-IP` only from explicitly configured trusted proxy IP literals. Such a proxy must overwrite the header. Strict IPv4/IPv6 parsing performs no DNS lookup. The filter preserves the original peer and path across standard servlet wrappers and rejects unsafe forwarding strategies. Production uses monotonic time; regression tests control time explicitly.

Keep forwarding strategy `NONE`. This process-local limiter covers selected hot endpoints and is not a general denial-of-service defense.

## Reproduction and evidence

[SHM API Guard](https://github.com/KarlLee123/shm-api-guard) builds the actual backend and runs its Spring controllers, services and MyBatis stack with synthetic H2 data on loopback. The historical snapshot must reproduce two forwarding-header findings while passing five input/data checks; the fixed snapshot must pass all seven. In current-source mode, any finding, failure, missing check or inconclusive result fails the gate.

The [application workflow](../../.github/workflows/shm-api-guard.yml) pins the tool and validates the actual source event SHA, with source cleanliness and built/loaded artifact checks. The [backend tests](../../shm-backend-fable5-copy/src/test/java/com/example/shm/common/ratelimit) cover strict literals, proxy trust and deterministic refill behavior. Reports produced by the new runs carry new repository commits; historical execution reports are not relabeled as new results.

Security maintenance and reporting follow [SECURITY.md](../../SECURITY.md).

## Source provenance

[provenance.json](provenance.json) maps the original research identifiers to the newly imported source snapshots. The [source comparison](remediation-comparison.diff) is generated from those imported snapshots and shows the preserved correction and regression tests; it is not a claim that the fix was newly written during republication. Original PR/advisory records and full Git mirrors are retained in a verified local backup before the previous repositories are retired.

## Verified runs after republication

[Application CI run 37923236336](https://github.com/hang4309/shm-os265/actions/runs/37923236336) ran SHM API Guard commit `01121bf17dbe51357b11e6902d57dbb648d94393` against actual application commit `92f1f44c2ebbc53d78ce12cfac9fe3dcfae3090b`. All 87 backend tests and seven real API checks passed. The [unchanged downloaded report](current-source-report.json) records that source and tool identity, clean worktrees, and matching built/loaded JAR hashes.

[Validator CI](https://github.com/KarlLee123/shm-api-guard/actions/runs/37923101219) passed 49 Python tests, verified the complete historical snapshot, reproduced its two expected header findings and passed all seven checks against the fixed snapshot. [Reports](https://github.com/KarlLee123/shm-api-guard/blob/main/reports/shm-sec-001.md#verified-runs-after-republication).

The continued disclosure is published as [GHSA-hhx2-qjv9-3j82](https://github.com/hang4309/shm-os265/security/advisories/GHSA-hhx2-qjv9-3j82). It describes the existing finding and original provenance; no new discovery is claimed.
