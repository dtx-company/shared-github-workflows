# ECR Migration

v1 included the migration from JFrog Artifactory to AWS ECR for Docker artifact hosting.

Teams have the option of updating in smaller steps or going straight to the end.

* v0.0.1 : safe starting point, tagged before any changes were made
* v0.1.0 : dual push ECR/JFrog, pull from JFrog
* v0.2.0 : dual push ECR/JFrog, pull ECR
* v1 : push/pull ECR, JFrog variables removed

All repos have initially been tagged at `0.0.1` or `v0.0.1`.

## v0.1.0

In your repo in the `.github/workflows` directory, look for the yaml files containing your workflows. You should see the line that sources the `shared-github-workflows` repo, example :

```
uses: dtx-company/shared-github-workflows/composite-actions/docker-build-and-push@v0.0.1
```

You will want to update the tag to `v0.1.0`, and add the below variables (selecting PROD or SDLC based on environment) :

```
ECR_FLOWCODE_FC_DOCKER_SERVER: ${{ secrets.ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SERVER }}
ECR_FLOWCODE_FC_DOCKER_KEY: ${{ secrets.ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_KEY }}
ECR_FLOWCODE_FC_DOCKER_SECRET: ${{ secrets.ECR_FLOWCODE_[PROD|SDLC]_FC_DOCKER_SECRET }}
```

Additionally, if previously you relied on the nesting functionality of JFrog (i.e. you stored images at service/env/image instead of service/image) then you will need to set a prefix, example :

```
ecr-image-path: prod-
```

Once this is run, you can verify it worked by looking for your image in `fc-commons-[prod|sdlc]` AWS account > ECR > Your repo (named by your service).

## v0.2.0

In your workflow file you will want to update the tag to `v0.2.0`.

If you have skipped to here, make sure you've added the variables from the previous step.

One this has run, you can verify it worked by looking at the image being used in ArgoCD, it should have an AWS path now instead of JFrog.

## v1

In your workflow file you will want to update the tag to `v1`

If you have skipped to here, make sure you've added the variables from the previous step(s), and remove the below variables :

```
JFROG_FLOWCODE_SERVER_NAME
JFROG_FLOWCODE_FC_DOCKER_USERNAME
JFROG_FLOWCODE_FC_DOCKER_PASSWORD
JFROG_FLOWCODE_FC_DOCKER_REPO
docker-image-path
```

Once this has run, you can verify it worked by checking the image created and making sure it is `not` in JFrog.
