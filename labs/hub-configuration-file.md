# Create a Hub Configuration File

In this section you will generate a [hub configuration file](https://hub.github.com/hub.1.html#CONFIGURATION) to hold the GitHub credentials generated in the [prerequisites lab](prerequisites.md#generate-a-github-api-token). The hub configuration file is used by the `hub` command when automating GitHub related tasks.

## Generate the Hub Configuration File

Create a hub configuration file in the current directory:

```
cat <<EOF > hub
github.com:
  - protocol: https
    user: ${GITHUB_USERNAME}
    oauth_token: $(cat .pipeline-tutorial-github-api-token)
EOF
```

Set the `HUB_CONFIG` environment variable to point to the generated hub configuration file:

```
HUB_CONFIG="${PWD}/hub"
```

## Encrypt the Hub Configuration File and Upload to S3

In this section you will encrypt the hub configuration file using the [AWS Key Management Service](https://aws.amazon.com/kms/) (KMS) and upload the encrypted file to a [Amazon S3](https://aws.amazon.com/s3) bucket. Performing these tasks will make the hub configuration file securely available to any automated build steps performed by Drone.

### Create a KMS Encryption Key

A KMS encryption key is required to encrypt the hub configuration file.

Generate a new encryption key (copy the "KeyArn" returned from this command):

```
aws kms create-key --description github
```

Create an alias for the encryption key (uses the "KeyArn" from previous command):

```
aws kms create-alias \
  --alias-name alias/github \
  --target-key-id <KeyARN>
```

Encrypt the hub configuration file using the `github` encryption key:

```
aws kms encrypt \
  --key-id "alias/github" \
  --plaintext "fileb://${HUB_CONFIG}" \
  --query CiphertextBlob \
  --output text > hub.enc
```

The encrypted hub configuration file `hub.enc` is now available in the current directory.

### Upload the Encrypted Hub Configuration File to S3

In this section you will create a S3 bucket and upload the encrypted hub configuration file to it.

Determine a unique bucket name and store it in the `S3_HUB_CONFIG_BUCKET` env var:

```
S3_HUB_CONFIG_BUCKET=example-pipeline-configs
```

Create a S3 bucket that will hold the encrypted hub configuration file:

```
aws s3 mb s3://${S3_HUB_CONFIG_BUCKET}
```

Upload the encrypted hub configuration file to the pipeline configs S3 bucket:

```
aws s3 cp hub.enc gs://${S3_HUB_CONFIG_BUCKET}/
```

### Grant the Drone CI Service Account Access to the GitHub Encryption Key

In this section you will grant access to the `github` encrypt key to the Drone CI service account. Performing these steps will enable Drone CI to decrypt the hub configuration file during any automated build.

Copy the following into `policy-document.json`, replacing `<<KeyArn>>` with your KeyArn from above:

```
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": [
            "kms:Encrypt",
            "kms:Decrypt"
        ],
        "Resource": [
            "<KeyArn>"
        ]
    }
}
```

Create an IAM policy for access to the KMS Key (save the "PolicyArn" from the response):

```
aws iam create-policy \
  --policy-name kms-github-key \
  --policy-document file://policy-document.json
```

Grant the Drone service account access to the `github` encryption key:

There are a number of ways to do this, but we will assume that you've created a `drone-service-role` role and already assigned that role to the Drone CI instance(s).

Attach Role Policy

```
aws iam attach-role-policy \
  --role-name drone-service-role \
  --policy-arn <PolicyArn>
```

There is also `attach-group-policy` and `attach-user-policy`, depending on your particular setup.

Next: [Setup the GitHub Repositories](github-repositories.md)
