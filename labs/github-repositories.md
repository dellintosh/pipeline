# Setup the GitHub Repositories

In this section you will create a set of GitHub repositories which hold the Drone CI [build pipelines](http://docs.drone.io/pipelines/) and the pipeline application code.

## Fork the Pipeline Application and Infrastructure Repositories

In this section you will fork the following GitHub repositories to your own GitHub account:

* [pipeline-application](https://github.com/dellintosh/pipeline-application)
* [pipeline-application-infra-staging](https://github.com/dellintosh/pipeline-application-infra-staging)
* [pipeline-application-infra-qa](https://github.com/dellintosh/pipeline-application-infra-qa)
* [pipeline-application-infra-production](https://github.com/dellintosh/pipeline-application-infra-production)

> Take a moment to review the each GitHub repository and step through the directory structure.

Set the list of repository names:

```
REPOS=(
  pipeline-application
  pipeline-application-infra-staging
  pipeline-application-infra-qa
  pipeline-application-infra-production
)
```

Fork the pipeline application and infrastructure repositories:

```
for repo in ${REPOS[@]}; do
  hub clone "https://github.com/dellintosh/${repo}.git"
  cd ${repo}/
  hub fork
  cd -
  rm -rf ${repo}
done
```

At this point the pipeline application and infrastructure repositories have been forked to your GitHub account and can used as part of your own deployment pipeline.


Next: [Drone CI Build Pipeline](build-pipeline.md)