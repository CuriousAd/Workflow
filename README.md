# Workflow

This repository contains a GitHub Actions workflow that adds occasional activity to
`daily-log.txt` and commits the change back to the `main` branch.

## Cron job

The workflow is checked once per day by GitHub Actions at **04:15 UTC**:

```cron
15 4 * * *
```

The cron schedule only starts the workflow. It does not guarantee a commit every
day. Scheduled runs first make a random decision; runs that do not pass the
decision finish without changing the repository. A manual `workflow_dispatch`
run always proceeds and creates a commit.

## Random activity algorithm

The workflow uses Bash's built-in `$RANDOM` pseudo-random number generator. It is
not a cryptographic random generator and is only intended to vary the activity
schedule.

For scheduled runs, the algorithm is:

1. Generate a pseudo-random integer with `$RANDOM`.
2. Calculate `$RANDOM % 100`, producing a value from `0` to `99`.
3. Commit when the value is less than `45` (approximately a 45% chance).
4. Otherwise, skip the log update and commit for that day.

When a commit is selected, the workflow also waits for a random number of seconds
from `0` through `1800` (up to 30 minutes) before updating the log. This makes
commit times less uniform.

The generator is not seeded or persisted between workflow runs. Its output is
therefore suitable for casual scheduling variation, but it should not be used for
security, authentication, or any decision requiring unpredictable randomness.
