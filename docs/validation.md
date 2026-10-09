# Validation and reproducibility

This repository republishes the existing SHM implementation with fresh Git history. The current source retains the reviewed forwarding-header fix and deterministic security regressions. Historical engineering screenshots and architecture documentation describe the original synthetic demonstration; they do not identify a new repository commit or substitute for a current test run.

## Backend and security checks

From `shm-backend-fable5-copy`, run `./mvnw -B verify` (`mvnw.cmd -B verify` on Windows). The suite uses isolated H2 data and loopback HTTP; no operational database is needed. See [the security verification record](security-verification.md).

The [SHM API Guard workflow](../.github/workflows/shm-api-guard.yml) checks the actual event commit and produces a report containing the source SHA, pinned tool SHA, built/loaded JAR hashes and seven real API checks. The [adoption record](security-tool-adoption.md) explains the trust boundary.

The [security record](security/SHM-SEC-001.md) distinguishes the historical vulnerable snapshot from the republished fixed code. New execution reports and GitHub Actions runs use new repository identities and are recorded there after verification.

## Collector and frontend commands

```sh
python -B -m unittest discover -s tools/demo -p test_demo.py -v
cd shm-frontend-fable5-copy
npm ci
npm run build
```

These commands describe additional components; the security workflow does not claim to validate device hardware, operational data, or every frontend behavior. See the [runbook](runbook.md) for the complete synthetic application demonstration.

## Source and notices

The original SHM code and documentation use the [MIT License](../LICENSE). Third-party notices are preserved in [THIRD_PARTY_NOTICES.md](../THIRD_PARTY_NOTICES.md).
