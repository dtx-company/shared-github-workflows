# Releasing shared-github-workflows

We use semantic versioning to tag SGW as we add features and patches, and also provide a `v1` tag that must be updated to the newest tag / commit.

It's good to save the short commit hash that v1 is set to before you update it, just in case you need to [roll back](#whoops-i-need-to-roll-back-a-release).

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

## Whoops, I need to roll back a release


* Locally delete the old tag

```
git tag -d v1
```

* Tag using the commit that you would like to go back to

``
git tag v1 abc123
```

* Force push the tag to remote

```
git push -f origin v1
```
