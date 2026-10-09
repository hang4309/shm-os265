# Historical vulnerable SHM backend regression snapshot

This branch imports an existing pre-remediation SHM backend snapshot for authorized, isolated security regression tests. It is not a new vulnerability or a deployable release. The fixed application is on the main branch.

The backend files are byte-for-byte copies of the historical source captured before repository republication. SNAPSHOT.json records their content hashes. New Git commits identify this import; no previous Git history or metadata is inherited. The tests build it on loopback with synthetic data through SHM API Guard, not an operational database.

The historical tests depended on a running database, so the baseline integration skips those old tests and exercises the actual API through the isolated harness. The expected observations are five input/data checks passing and two forwarding-header rate-limit bypass findings. Fixed and current modes require all checks to pass.
