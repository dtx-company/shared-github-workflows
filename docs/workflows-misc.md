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

## get-image-tag
Utility action that extracts the current image tag(version) from an
ArgoCD/fc-service chart based deployment and makes it available to other jobs
within the workflow.

### Inputs
The following inputs are used to find the deployment definition.  They are used
to build a path to the values.yaml file in the specified deployment repo.

| Input | Desciption | Example | Required | Default |
|---|---|---|---|---|
| account | The account the deployment is targeting | fc-services-prod | Y | '' |
| service | The service being deployed | Y | '' |
| project | The team/project owning the deployment | Y | '' |
| region  | Our regional shorthand value | N | use1 |
| repository | The repo containing the deployment definition | N | fc-infra-kubernetes |
| runner | The runner on which the action will run | N | self-hosted |


The following example retrieves the current image tag for the deplopment for
our projection flow frontend

<!-- markdown-link-check-disable -->
- Repository: [fc-infra-kubernetes](https://github.com/dtx-company/fc-infra-kubernetes)
- Path: [cloud/aws/fc-services-prod/use1/primary/frontend/applications/flow-app](https://github.com/dtx-company/fc-infra-kubernetes/tree/trunk/cloud/aws/fc-services-prod/use1/primary/frontend/applications/flow-app)
<!-- markdown-link-check-enable -->

Note the `with:` block which sets the input values

```
name: get-image-tag
on:
  workflow_dispatch:
jobs:
  get-image-tag:
    name: get-image-tag
    uses: dtx-company/shared-github-workflows/.github/workflows/get-image-tag.yaml@v1
    with:
      account: fc-services-prod
      service: flow-app
      project: frontend
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
  print-image-tag:
    runs-on: self-hosted
    needs: get-image-tag
    steps:
      - run: echo ${{needs.get-image-tag.outputs.imageTag}}
```
## Full Examples
<!-- markdown-link-check-disable -->
See https://github.com/dtx-company/screenshot-service/tree/master/.github/workflows for live examples.
<!-- markdown-link-check-enable -->
