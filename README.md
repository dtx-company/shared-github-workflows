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

## build-and-push-and-nonprod-release.yaml
This workflow should be added to all services that wish to be continuously deployed to fc-services-stg and fc-services-preprod.

Prerequisites:
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `JFROG_FLOWCODE_SERVER_NAME`, `JFROG_FLOWCODE_FC_DOCKER_USERNAME`, `JFROG_FLOWCODE_FC_DOCKER_PASSWORD`, `JFROG_FLOWCODE_FC_DOCKER_REPO`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes under fc-services-stg and fc-services-preprod.
* If the service is a Go service, some changes may be required to the Dockerfile. See what was done for [payments](https://github.com/dtx-company/payments/pull/107/files). Test the Dockerfile changes by running `docker build .`. A failing build might result in an error like "COPY failed"; a successful build will result in no error.

To integrate the shared workflow into the service:
* Add a new workflow in the service repo under `.github/workflows/build-and-push-and-nonprod-release.yaml`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
* Replace `<service-name>` with the name of the service, which should match the repository name exactly.
* Replace `<project-name>` with one of {backend, frontend, verticals, ml}.
```
name: build-and-push-and-nonprod-release
on:
  push:
    branches:
      - <default-branch>
jobs:
  cd:
    name: shared-nonprod-cd-workflow
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-nonprod-release.yaml@v1
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
      ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SERVER }}
      ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_KEY }}
      ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SECRET }}
```


## build-and-tag-and-prod-release.yaml
This workflow should be added to all services that wish to be deployed to fc-services-prod. This workflow is intended to be triggered through manual dispatch. The workflow uses the [semantic-release](https://semantic-release.gitbook.io/semantic-release/) tool.

Prerequisites:
* Make sure the service has already integrated the conventional-pr-title workflow so that conventional commits are being enforced.
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SERVER`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_KEY`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SECRET`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes under fc-services-prod.

To integrate the shared workflow into a service, add two files.

Add a new file into the root of the service repo called `.releaserc`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
* Replace `<service-name` with the name of the service, which is the same as the repository name.
```
{
  "repositoryUrl": "https://github.com/dtx-company/<service-name>.git",
  "branches": [
    <default-branch>
  ],
  "plugins": [
    "@semantic-release/commit-analyzer",
    "@semantic-release/release-notes-generator",
    "@semantic-release/github"
  ]
}
```

Add a new workflow in the service repo under `.github/workflows/build-and-tag-and-prod-release.yaml`, with the content below.
* Replace `<service-name>` with the name of the service, which should match the repository name exactly.
* Replace `<project-name>` with one of {backend, frontend, verticals, ml}.


```
name: build-and-tag-and-prod-release
on:
  workflow_dispatch:
jobs:
  cd:
    name: shared-prod-cd-workflow
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-tag-and-prod-release.yaml@trunk
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
      ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_PROD_FC_DOCKER_SERVER }}
      ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_PROD_FC_DOCKER_KEY }}
      ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_PROD_FC_DOCKER_SECRET }}
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

- Repository: [fc-infra-kubernetes](https://github.com/dtx-company/fc-infra-kubernetes)
- Path: [cloud/aws/fc-services-prod/use1/primary/frontend/applications/flow-app](https://github.com/dtx-company/fc-infra-kubernetes/tree/trunk/cloud/aws/fc-services-prod/use1/primary/frontend/applications/flow-app)

Note the `with:` block which sets the input values

```
name: get-image-tag
on:
  workflow_dispatch:
jobs:
  get-image-tag:
    name: get-image-tag
    uses: dtx-company/shared-github-workflows/.github/workflows/get-image-tag.yaml@feature/sc-52937/support-argocd-able-to-trigger-a-github-actions
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
See https://github.com/dtx-company/screenshot-service/tree/master/.github/workflows for live examples.
