# shared-github-workflows
Reusable GitHub Actions workflows.

## conventional-pr-title.yaml
This workflow should be added to all services to ensure that pull request titles (and by extension, squashed commit messages in the default branch) follow the conventional commit standard. This is important because other tooling will assume conventional commits in order to autogenerate release notes and bump up the semantic version on every release to production.

Prerequisites:
* None

To integrate the shared workflow into the service:
* Add a new workflow in the service repo under `.github/workflows/conventional-pr-title.yaml`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
```
name: conventional-pr-title
on:
  pull_request:
    branches:
      - <default-branch>
    types: # See https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows#pull_request
      - opened
      - edited
      - synchronize
      - reopened
jobs:
  validate:
    name: validate-pr-title
    uses: dtx-company/shared-github-workflows/.github/workflows/conventional-pr-title.yaml@v1
```
