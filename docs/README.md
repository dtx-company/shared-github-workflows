# shared-github-workflows
Reusable GitHub Actions workflows and composite actions.

Index :

* [Deploy Workflows](workflows-deploy.md)
  * [Deprecated Deploy Workflows](workflows-deploy-DEPRECATED.md)
* [Misc Workflows](workflows-misc.md)
* [Composite Actions](composite-actions.md)

How to Use :

Repos which would like to be automatically built should call one of the relevant Workflow files in this repo. The Workflow files reference different Composite Actions which provide packaged functionality and abstract away some of the complexities of managing the backends of builds.

![Relationship Between Workflows and Composite Actions](images/SGW-structure.png)
