# shared-github-workflows
Reusable GitHub Actions workflows.

## build-and-push-and-nonprod-release.yaml
This workflow should be added to all services that wish to be continuously deployed to fc-services-stg and fc-services-preprod.

Prerequisites:
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `JFROG_FLOWCODE_SERVER_NAME`, `JFROG_FLOWCODE_FC_DOCKER_USERNAME`, `JFROG_FLOWCODE_FC_DOCKER_PASSWORD`, `JFROG_FLOWCODE_FC_DOCKER_REPO`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes.

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
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-nonprod-release.yaml@trunk
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      JFROG_FLOWCODE_SERVER_NAME: ${{ secrets.JFROG_FLOWCODE_SERVER_NAME }}
      JFROG_FLOWCODE_FC_DOCKER_USERNAME: ${{ secrets.JFROG_FLOWCODE_FC_DOCKER_USERNAME }}
      JFROG_FLOWCODE_FC_DOCKER_PASSWORD: ${{ secrets.JFROG_FLOWCODE_FC_DOCKER_PASSWORD }}
      JFROG_FLOWCODE_FC_DOCKER_REPO: ${{ secrets.JFROG_FLOWCODE_FC_DOCKER_REPO }}
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
```

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
    uses: dtx-company/shared-github-workflows/.github/workflows/conventional-pr-title.yaml@trunk
```

## prod-release.yaml
Coming soon ...

## Full Examples
See https://github.com/dtx-company/screenshot-service/tree/master/.github/workflows for live examples.
