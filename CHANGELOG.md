# Changelog

All notable changes to this project will be documented in this file.

A pretty print of the commit lines can be obtained by :

```
git log --pretty="- %s" >> CHANGELOG.md
```

## v1.1.0

Updating Github Actions to fix the `set-output` deprecations.

### Added

### Fixed

- bumping versions

### Updating

n/a

## v1.0.1

Changing the git config setup for nonprod to reflect prod setup (which accomodates bot interactions better).

### Added

- update: adding testing info (#68)
- Updating README (#63)

### Fixed

- Chore/sc 70095/resolve conflict w mergify and non prod release (#69)
- chore: moving readme location (#65)
- moving off self-hosted because of public repo

### Updating

N/a

## v1.0.0

This marks the final stage of the JFrog Artifactory to ECR migration, where push/pull is from ECR only and JFrog Artifactory related variables are removed.

### Added

- Feature/sc 65840/shared workflows v1 0 0 release ga push pull (#61)

### Fixed

### Updating

Remove JFrog variables :

* `JFROG_FLOWCODE_SERVER_NAME`
* `JFROG_FLOWCODE_FC_DOCKER_USERNAME`
* `JFROG_FLOWCODE_FC_DOCKER_PASSWORD`
* `JFROG_FLOWCODE_FC_DOCKER_REPO`
* `docker-image-path`

## v0.2.0

Switches the workflows to pull Docker images from ECR instead of JFrog.

### Added

- update: moving to ECR pull (#60)

### Fixed

### Updating

## v0.1.0

Switches the workflows to dual push Docker images to both ECR and JFrog.

### Added

- update: switching to dual push, jfrog pull (#59)

### Fixed

### Updating

Add ECR variables :

* `ECR_FLOWCODE_FC_DOCKER_SERVER`
* `ECR_FLOWCODE_FC_DOCKER_KEY`
* `ECR_FLOWCODE_FC_DOCKER_SECRET`
* (optional) `ecr-image-path`
