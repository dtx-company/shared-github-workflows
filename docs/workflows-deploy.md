# Github Actions Deploy Workflows

Reusable GitHub Actions Workflows for deploys.

## build-and-push-and-deploy.yaml
This workflow should be added to all services that wish to be continuously deployed **without** a release. By default deploys to both staging and preprod.

<!-- markdown-link-check-disable -->
Prerequisites:
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SERVER`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_KEY`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SECRET`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes under all relevant fc-services-ENVs
* If the service is a Go service, some changes may be required to the Dockerfile. See what was done for [payments](https://github.com/dtx-company/payments/pull/107/files). Test the Dockerfile changes by running `docker build .`. A failing build might result in an error like "COPY failed"; a successful build will result in no error.
<!-- markdown-link-check-enable -->

To integrate the shared workflow into the service:
* Add a new workflow in the service repo under `.github/workflows/name: build-and-push-and-deploy-nonprod.yaml`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
* Replace `<service-name>` with the name of the service, which should match the repository name exactly.
* Replace `<project-name>` with one of {backend, frontend, verticals, ml}.
```
name: build-and-push-and-deploy-nonprod
on:
  push:
    branches:
      - <default-branch>
jobs:
  cd:
    name: shared-nonprod-cd-workflow
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-deploy.yaml@v1
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
      ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SERVER }}
      ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_KEY }}
      ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SECRET }}
```

## build-and-push-and-deploy-release.yaml
This workflow should be added to all services that wish to be continuously deployed **with** a release. By default deploys to both staging and preprod.

<!-- markdown-link-check-disable -->
Prerequisites:
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SERVER`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_KEY`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SECRET`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes under all relevant fc-services-ENVs
* If the service is a Go service, some changes may be required to the Dockerfile. See what was done for [payments](https://github.com/dtx-company/payments/pull/107/files). Test the Dockerfile changes by running `docker build .`. A failing build might result in an error like "COPY failed"; a successful build will result in no error.
<!-- markdown-link-check-enable -->

To integrate the shared workflow into the service:
* Add a new workflow in the service repo under `.github/workflows/name: build-and-push-and-deploy-nonprod.yaml`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
* Replace `<service-name>` with the name of the service, which should match the repository name exactly.
* Replace `<project-name>` with one of {backend, frontend, verticals, ml}.
```
name: build-and-push-and-deploy-nonprod
on:
  push:
    branches:
      - <default-branch>
jobs:
  cd:
    name: shared-nonprod-cd-workflow
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-deploy-release.yaml@v1
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
      ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SERVER }}
      ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_KEY }}
      ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SECRET }}
```

## build-and-push-and-deploy-flow.yaml
A flow-specific workflow that adds on support for postbuild script and additional variables (BUF_TOKEN, PYPI). By default deploys to both staging and preprod.

<!-- markdown-link-check-disable -->
Prerequisites:
* Make sure the service repo has granted read permission to the `fc-cicd-rw` Github user. This user is already a part of the `dtx` team so it is sufficient if the `dtx` team has the required permissions.
* Make sure the service repo has access to the necessary organization secrets. Check in Settings -> Actions -> Secrets in the repsitory settings. Must have: `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SERVER`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_KEY`, `ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SECRET`, and `PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD`.
* Make sure the service has helm charts in https://github.com/dtx-company/fc-infra-kubernetes under all relevant fc-services-ENVs
* If the service is a Go service, some changes may be required to the Dockerfile. See what was done for [payments](https://github.com/dtx-company/payments/pull/107/files). Test the Dockerfile changes by running `docker build .`. A failing build might result in an error like "COPY failed"; a successful build will result in no error.
<!-- markdown-link-check-enable -->

To integrate the shared workflow into the service:
* Add a new workflow in the service repo under `.github/workflows/name: build-and-push-and-deploy-nonprod.yaml`, with the content below.
* Replace `<default-branch>` with the default branch of the repo, likely `main`, `master` or `trunk`.
* Replace `<service-name>` with the name of the service, which should match the repository name exactly.
* Replace `<project-name>` with one of {backend, frontend, verticals, ml}.
```
name: build-and-push-and-deploy-nonprod
on:
  push:
    branches:
      - <default-branch>
jobs:
  cd:
    name: shared-nonprod-cd-workflow
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-deploy-flow.yaml@v1
    with:
      service: <service-name>
      project: <project-name>
    secrets:
      PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD: ${{ secrets.PERSONAL_ACCESS_TOKEN_GITHUB_WORKFLOWS_CICD }}
      ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SERVER }}
      ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_KEY }}
      ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_SDLC_FC_DOCKER_SECRET }}
```
