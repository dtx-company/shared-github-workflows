# Releasing shared-github-workflows

We use semantic versioning to tag SGW as we add features and patches, and also provide a `v1` tag that must be updated to the newest tag / commit.

To update :

* Update your local to the most recent commit
* Locally delete the old tag

```
git tag -d v1
```

* Tag the (up to date) local with the tag

```
git tag v1
```

* Force push the tag to remote

```
git push -f origin v1
```
