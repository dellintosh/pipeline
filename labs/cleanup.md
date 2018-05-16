# Cleanup

## Kubernetes Resources

Delete the Kubernetes clusters created in the [kubernetes lab](kubernetes-clusteres.md).  This will shutdown the clusters and schedule them for deletion.

## Delete GitHub Repositories

In this section you will cleanup the GitHub repositories created during this tutorial.

```
REPOS=(
  pipeline-application
  pipeline-application-infra-staging
  pipeline-application-infra-qa
  pipeline-application-infra-production
)
```

```
for repo in ${REPOS[@]}; do
  curl -X DELETE "https://api.github.com/repos/${GITHUB_USERNAME}/${repo}"
done
```