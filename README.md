# dashboard-et-runner

Scheduled jobs for [dashboard.et](https://www.dashboard.et). This repo is public so the jobs run on free GitHub-hosted runners; the app code stays in a private repo that each job checks out with a deploy key.

- `scheduled-ingest.yml`: hourly ingest into the Neon Postgres database.
- `static-snapshot-export.yml`: daily refresh of the public JSON snapshot, pushed back to the app repo and deployed by Vercel.

Don't edit the workflows here. Their source is `ops/runner/` in the app repo; publish changes with `ops/runner/sync.sh`.

Secrets: `APP_REPO_DEPLOY_KEY` (write deploy key on the app repo), `DATABASE_URL` (Neon **direct**, non-pooler URL). The ingestion lease uses session-level advisory locks.

Activation order:

1. Merge the app changes first: both workflows check out app `main`.
2. Configure the deploy key and direct database URL in this runner repo. Never put either value in a workflow file.
3. Apply migrations and seed the new database once, then update the API host to use the same database.
4. Publish these workflows with `ops/runner/sync.sh` while the public workflows remain disabled.
5. Enable and manually run each public workflow; confirm ingestion commits a fresh API batch and the snapshot job publishes fresh artifacts with the ESX check passing.
6. Disable the old private-repo schedules only after those runs pass. Keep them available for rollback until the cutover is verified.

On 2026-10-26 the public workflows were disabled and neither required secret was configured. `/api/status` reported a stale July batch. This is preparation, not evidence of a completed Neon cutover.
