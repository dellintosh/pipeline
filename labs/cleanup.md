# Cleanup

## Kubernetes Resources

Delete the Kubernetes clusters created in the [kubernetes lab](kubernetes-clusters.md).  This will shutdown the clusters and schedule them for deletion.

```
ENVIRONMENTS=(
  staging
  qa
  production
)
```

Run the `kops delete cluster` command:

```
for e in ${ENVIRONMENTS[@]}; do
  kops delete cluster --name ${e}
done
```

Verify that the information returned is correct, then to *PERMANENTLY DELETE* the Kubernetes clusters:

```
for e in ${ENVIRONMENTS[@]}; do
  kops delete cluster --name ${e} --yes
done
```

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