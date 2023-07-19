# Composite Actions

Reusable composite actions.

## How to Use

Composite actions are used within Workflows by including the following reference :

```
jobs:
  cd:
    runs-on: [self-hosted, "${{ inputs.runner }}"]
    steps:
      - name: NAME_FOR_STEP
        uses: dtx-company/shared-github-workflows/composite-actions/COMPOSITE_ACTION_NAME@v1
        with:
          VARIABLE: VALUE
          VARIABLE: ${{ input.INPUT }}
          VARIABLE: ${{ secret.SECRET}}
```

An example would be :

```
jobs:
  cd:
    runs-on: [self-hosted, "${{ inputs.runner }}"]
    steps:
      - name: Setup Image Repository
        uses: dtx-company/shared-github-workflows/composite-actions/setup-image-repository@v1
        with:
          service: ${{ inputs.service }}
          ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_FC_DOCKER_SERVER }}
          ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_FC_DOCKER_KEY }}
          ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_FC_DOCKER_SECRET }}
```

## setup-image-repository

This composite action handles setting up both the image repository (if not already present) and access to the image repository for future steps. Please see the `action.yaml` file for variables.

## setup-docker

This composite action handles setting up the environment to run Docker in. Please see the `action.yaml` file for variables.

## docker-build-and-push

This composite action handles building a Docker image and pushing it to an image repository. Please see the `action.yaml` file for variables.

Prerequisites :
* setup-image-repository
* setup-docker

Alternatives :
* docker-build-and-push-flow : flow repo customized action, includes additional variables for build
* docker-build-and-push-flow-legacy : DEPRECATED, legacy flow repo customized workflow

## extract-and-upload-static-assets-flow

This composite action handles extracting static assets from the flow Docker image and uploading them to S3. Please see the `action.yaml` file for variables.

Prerequisites :
* docker-build-and-push-flow

## DEPRECATED run-postbuild-script-flow

DEPRECATED This composite action handles extracting static assets from the flow Docker image and uploading them to S3. Please see the `action.yaml` file for variables.

## update-infra-kubernetes

This composite action handles updating a `*-infra-kubernetes` styled repo with a new image tag for a service, specifically by updating the yaml configurations present there. Please see the `action.yaml` file for variables.

Prerequisites :
* docker-build-and-push(-flow)
