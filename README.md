# shelf-watch

Scheduled checks. No application code, no data, and nothing that identifies
what they check.

## Why this is a separate repo

Actions minutes bill to the account that owns the repository, and standard
runners are free on public repositories. The code has to stay private, so the
workflows live here and mount it at `code/` with a read-only deploy key.

## What runs

| workflow | cadence | asks | red when |
|---|---|---|---|
| `build-check` | nightly | do the private repo's tests pass on main | a test fails |
| `source-watch` | daily | is each upstream still publishing, and is our sync keeping up | one stays ahead of us past the window, or none can be measured |
| `freshness` | daily | did the sync run, and run clean | its record is missing, stale or carries errors |
| `watchdog` | daily | did every scheduled check run and reach its code | any did not |

A source that grew is the good case and stays green.

## Nothing here is identifying, including in the logs

Everything specific arrives at run time as a secret: `CODE_REPO` (owner/name of
the private repo, a secret so the checkout log masks it), `CODE_DEPLOY_KEY`
(read-only), `RESEND_API_KEY`, `ALERT_EMAIL`, `EMAIL_FROM`.

- Commands run through `quiet.sh`: the log shows exit codes and counts only.
- Detail goes by mail on failure (`scripts/mail_failure.py`, standard library only).
- No artifacts: an artifact on a public repo is world-downloadable.
- Every publish passes a leak scan kept in the private repo.
