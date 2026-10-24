# Lab 5 · Your first CI pipeline

GitHub user: ranbirsingh0091 · Reference files used (Lab 2 fallback): no

![ci](https://github.com/ranbirsingh0091/turiba-devops-labs/actions/workflows/ci.yml/badge.svg)

<!-- Keep every heading as it is: the grader finds the sections by name. -->

## 1 · Graph predictions

| # | Question | I predicted, and why | What the run showed |
|---|----------|----------------------|---------------------|
| P1 | Which jobs start together? | graph-build and graph-lint start together, because they have no `needs`. | Both started together at the beginning of the run. |
| P2 | Will `graph-package` find `build.txt`? | No, because jobs run on different virtual machines. `graph-test` downloads it as an artifact, but `graph-package` has no download step. | `build.txt: not here: jobs don't share files` |
| P3 | With `break = build`: which jobs run, which fail, which are skipped? | graph-build fails. graph-test and graph-package are skipped. graph-report runs because it has `if: always()`. | graph-build failed (red). graph-test and graph-package were skipped (grey). graph-report ran and succeeded (green). |

## 2 · Pipeline

- B5: I predicted **localhost** because the steps run directly on the runner VM.
- My first red run in Part 2: **The integration job failed because of a typo in the secret name (`DB__PASSWORD` instead of `DB_PASSWORD`).**
- What I changed to make it green: **Fixed the secret, fixed the YAML syntax, and added the 404 bug fix to server.js.**
- My lint findings, and how I fixed them: **'server' is assigned a value but never used. I added the graceful shutdown handler for SIGTERM and SIGINT.**
- Part 2 pull request (URL): **https://github.com/ranbirsingh0091/turiba-devops-labs/pull/3**
- Part 3: will the PR run push an image? I predicted **No**.
- My image on GHCR: `ghcr.io/ranbirsingh0091/turiba-devops-labs/api:8cfa933`

## 3 · Red PR

- Red PR (URL): **https://github.com/ranbirsingh0091/turiba-devops-labs/pull/3**
