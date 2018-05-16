# Provision Kubernetes Clusters

In this section you will create a Kubernetes cluster using [Kubernetes Kops](https://github.com/kubernetes/kops) for each of the following environments:

* staging
* qa
* production

Using dedicated Kubernetes clusters provides better isolation between environments which can result in simpler cluster configurations.

## Create the Kubernetes Clusters

Set the list of environments:

```
ENVIRONMENTS=(
  staging
  qa
  production
)
```

Create a Kubernetes clusters for each environment

Please see these guides for installation instructions:

* [Kubernetes Getting Started with Kops](https://kubernetes.io/docs/getting-started-guides/kops/)
* [Kops documentation](https://github.com/kubernetes/kops)

Basic cluster creation:

```
for e in ${ENVIRONMENTS[@]}; do
  kops create cluster --v=0 \
    --cloud=aws \
    --node-count=1 \
    --master-size=t2.medium \
    --master-zones=us-east-1a \
    --zones us-east-1a,us-east-1b \
    --name=${e} \
    --node-size=m3.xlarge \
    --ssh-key=~/.ssh/id_rsa.pub \
    --dns-zone ${e}.example.com 2>&1 | tee create_cluster-${e}.txt
done
```

It can take up to five minutes to provision the Kubernetes clusters. Use the `kops` command to check the status of each cluster:

```
kops get clusters
```
```
NAME        CLOUD       ZONES
staging     aws         us-east-1a,us-east-1b
qa          aws         us-east-1a,us-east-1b
production  aws         us-east-1a,us-east-1b
```

Next: [Create a Hub Configuration File](hub-configuration-file.md)