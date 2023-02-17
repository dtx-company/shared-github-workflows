# Testing shared-github-workflows

Since the `shared-github-workflows` (SGW) repo is a shared repo across the org (and most build scripts are using a tag that will auto-update), it is extra important to test changes to it.

## Testing in a Live Build Pipeline

For this how-to, we are going to use the `https://github.com/dtx-company/flow` repo as the "Live Build Pipeline" repo. It already has Github Actions set up and using the SGW for multiple environments, and we will update it so it is pointing to a branch in SGW that we will make changes too.

If you are setting this up for your own repo for testing, replace all mentions of flow repo with your repo.

<!-- markdown-link-check-disable -->
* Create a branch from the existing SGW repo, ex. feature123
    * There is a bug in Github Actions that forward slashes (`/`) aren't usable in branches for sourcing
* In flow repo, update [the reference for SGW](https://github.com/dtx-company/flow/blob/9bedf430a615fe89fac62c18d788ab4994b43acb/.github/workflows/flow-stg-build-and-release.yaml#L11) to point to the branch you just made :
<!-- markdown-link-check-enable -->

```
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-nonprod-release.yaml@v1
```
to
```
    uses: dtx-company/shared-github-workflows/.github/workflows/build-and-push-and-nonprod-release.yaml@feature123
```

* Then commit / push / PR / merge as usual to flow repo
* Make and push changes to your SGW branch
* The next run in flow repo will then use these new changes
    * You can re-trigger Actions manually via Github Actions console, or by pushing a commit to the necessary branch
* Continue until you are satisfied with the changes, then revert the step pointing flow SGW reference to your branch

## Testing Independently and/or Components

It can be challenging to test outside of a pre-established repo because of how much the SGW build script handle. To help, we've set up a `/test` directory with a few smaller setups that can be used to test parts of the larger deploy process and/or test outside of a live pipeline.

### docker-build

In `test/docker-build` we have an action that can be used to test the deploy process up to and including the build step. It will build a Dockerfile and push it up to ECR.

Once you include a Dockerfile at the root of your repo, source this test via :

```
        uses: dtx-company/shared-github-workflows/test/docker-build@trunk
```
