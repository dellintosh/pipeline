# Drone CI Build Pipeline

In this section you will configure the [Drone CI](https://drone.io) build pipeline necessary to establish an [end-to-end build pipeline](deployment-pipeline.md).

## Prerequisites

Ensure that you have completed all steps in the [prerequisites lab](prerequisites.md)

## Build Pipeline Configurations

In this section you will update the necessary `.drone.yml` build pipeline configurations that will be used in the pipeline.

```
for e in ${ENVIRONMENTS[@]}; do
  hub clone ${GITHUB_USERNAME}/pipeline-application-infra-${e}
done
```

### Update the `pipeline-application` configuration

Replace the following values in the `pipeline-application/.drone.yml` file and add/commit/push:

* DOCKER_USERNAME - DockerHub username (create repository for `pipeline-application` and grant access)
* S3_KOPS_STATE_STORE - S3 Bucket name where Kops configurations are stored
* S3_HUB_CONFIG_BUCKET - S3 Bucket name where Hub credentials are stored
* GITHUB_ACCOUNT - GitHub email attached to Hub credentials

### Update the `pipeline-application-infra-staging` configuration

Replace the following values in the `pipeline-application-infra-staging/.drone.yml` file and add/commit/push:

* S3_KOPS_STATE_STORE - S3 Bucket name where Kops configurations are stored

### Update the `pipeline-application-infra-qa` configuration

Replace the following values in the `pipeline-application-infra-qa/.drone.yml` file and add/commit/push:

* S3_KOPS_STATE_STORE - S3 Bucket name where Kops configurations are stored
* S3_HUB_CONFIG_BUCKET - S3 Bucket name where Hub credentials are stored
* GITHUB_ACCOUNT - GitHub email attached to Hub credentials

## Enable Repositories in Drone CI

In this section you will enable the repositories in Drone CI:

```
drone repo sync
for e in ${ENVIRONMENTS[@]}; do
  drone repo add ${GITHUB_USERNAME}/pipeline-application-infra-${e} && \
  drone repo update --trusted ${GITHUB_USERNAME}/pipeline-application-infra-${e}
done
```

At this point, you should be able to login to your Drone CI server and verify that the repositories are enabled.  You are now ready to test the pipeline.

Next: [Test the Pipeline](test-the-pipeline.md)