# dashboard-et-runner

Scheduled jobs for [dashboard.et](https://www.dashboard.et). This repo is public so the jobs run on free GitHub-hosted runners; the app code stays in a private repo that each job checks out with a deploy key.

- `scheduled-ingest.yml`: hourly ingest into the Neon Postgres database.
- `static-snapshot-export.yml`: daily refresh of the public JSON snapshot, pushed back to the app repo and deployed by Vercel.

Don't edit the workflows here. Their source is `ops/runner/` in the app repo; publish changes with `ops/runner/sync.sh`.

Secrets: `APP_REPO_DEPLOY_KEY` (write deploy key on the app repo), `DATABASE_URL` (Neon pooled URL).
