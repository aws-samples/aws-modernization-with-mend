---
title: "Deployment"
chapter: true
weight: 151
---

# Mend Renovate

## Deploy Mend Renovate for CodeCommit

**[Mend Renovate for CodeCommit](https://docs.renovatebot.com/modules/platform/)** is distributed as a pre-built [Docker image](https://hub.docker.com/r/renovate/renovate), so it can be executed from any platform that supports Docker containers (any automation pipeline, or even your local terminal/command prompt). In this workshop, you will deploy Mend Renovate on AWS CodeBuild.  

{{% notice tip %}}
To deploy Mend Renovate for repositories other than CodeCommit, please refer to [Mend Renovate Platforms](https://docs.renovatebot.com/modules/platform/).
{{% /notice %}}

{{% notice tip %}}
The need to run **Mend Renovate** to periodically check for new package versions is not correlated with the number of commits (and therefore pipelines jobs) you have in your repository.  
It is recommended to execute **Mend Renovate** as a scheduled task from a central, dedicated CodeBuild project, so you don't have to configure it for each repository individually.  
{{% /notice %}}

<!-- ### Get AWS IAM Credentials
Access Key id and Secret access key id
https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html

Set up [Secrets Manager](https://console.aws.amazon.com/secretsmanager/home)
Mend/Creds

**TBC** -->


<!-- ### Set Up a Dedicated Build Project
In [CodeBuild](https://us-east-1.console.aws.amazon.com/codesuite/codebuild/projects), go to **Build** >> **Build projects** and click on **Create build project**.  

Populate the following parameters:  

- **Project configuration**
  - Project name: mend-renovate  
  - Description: Mend Renovate scheduled task  
- **Source**
  - Source provider: AWS CodeCommit
  - Repository: easybuggy
  - Branch: main
- **Environment**
  - Environment image: Managed image
  - Operating system: Amazon Linux 2
  - Role name: Mend-Renovate
- **Buildspec**
  - Build specifications: Insert build commands

In the **Build commands** box, insert the following:  

    version: 0.2

    env:
      shell: bash
      variables:
        RENOVATE_ENDPOINT: "https://git-codecommit.us-east-1.amazonaws.com/"
        RENOVATE_PLATFORM: "codecommit"
        RENOVATE_REPOSITORIES: ['easybuggy']
        RENOVATE_CONFIG: '{"onboardingConfig":{"extends":["github>whitesource/merge-confidence:beta","config:base"]}}'
      secrets-manager:
        AWS_ACCESS_KEY_ID: "Mend/Creds:AWS_ACCESS_KEY_ID"
        AWS_SECRET_ACCESS_KEY: "Mend/Creds:AWS_SECRET_ACCESS_KEY"
      exported-variables:
        - AWS_REGION
        
    phases:
      build:
        on-failure: CONTINUE
        commands:
          - docker run --rm -e AWS_REGION -e AWS_ACCESS_KEY_ID -e AWS_SECRET_ACCESS_KEY -e RENOVATE_CONFIG -e RENOVATE_ENDPOINT -e RENOVATE_PLATFORM -e RENOVATE_REPOSITORIES renovate/renovate


Click **Create build project**   -->


### Build Specification

{{% notice info %}}
For syntax reference and more information on build specification (buildspec) files, refer to [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) under AWS CodeBuild User Guide.
{{% /notice %}}

To integrate **Mend Renovate** with **AWS CodeBuild**, you'll need to update your build specification (`buildspec.yml`) file.  

If the file doesn't exist, create it under your project's root directory (if you do not have committer access to the source repo, you can also use the CodeBuild console to add build commands manually).  

![Update build specification](/images/mend-renovate/mend-renovate-update-buildspec.png)

#### Build Environment
Add the following variables to the buildspec's `env` section:

| Variable | Description | Value |
|:----|:----|:----|
| `RENOVATE_ENDPOINT` | Repository endpoint URL | `https://git-codecommit.us-east-1.amazonaws.com` |
| `RENOVATE_PLATFORM` | Repository platform type (see [supported platforms](https://docs.renovatebot.com/modules/platform/)) | `codecommit` |
| `RENOVATE_REPOSITORIES` | A list of repository names to scan | `['repo1', 'repo2']` |
| `RENOVATE_CONFIG` | Renovate recommended configuration (see [full reference](https://docs.renovatebot.com/self-hosted-configuration/)) | `'{"onboardingConfig": {"extends":` `["config:base"]}}'` |
| `AWS_REGION` | AWS region | `us-east-1` |

{{% notice tip %}}
The variable `AWS_REGION` is a built-in AWS environment variable, so there's no need to specify it, you can simply use the `exported-variables` node to use its value.  
{{% /notice %}}

{{% notice tip %}}
When utilizing the recommended configuration (`{"extends":["config:base"]}`), Mend Renovate will only create up to 2 PRs per hour, to avoid performance issues.  
This number can be changed by adding the environment variable `RENOVATE_PR_COMMITS_PER_RUN_LIMIT`.  
{{% /notice %}}

{{% notice tip %}}
Instead of specifying the repositories to scan (`RENOVATE_REPOSITORIES`), you can also utilize the [autodiscover]([autodiscover](https://docs.renovatebot.com/self-hosted-configuration/#autodiscover)) feature.  

{{% /notice %}}
<!-- ToDo - change these snippets to `importcode` shortcodes -->
```yaml
env:
  shell: bash
  variables:
    RENOVATE_ENDPOINT: "https://git-codecommit.us-east-1.amazonaws.com/"
    RENOVATE_PLATFORM: "codecommit"
    RENOVATE_REPOSITORIES: 'easybuggy'
    RENOVATE_CONFIG: '{"onboardingConfig":{"extends":["config:base"]}}'
  exported-variables:
    - AWS_REGION
```

#### Build Phases
Add the following to the `build` phase to execute the [Mend Renovate]():

```yaml
phases:
  build:
    on-failure: CONTINUE
    commands:
      - docker run --rm -e AWS_REGION -e RENOVATE_CONFIG -e RENOVATE_ENDPOINT -e RENOVATE_PLATFORM -e RENOVATE_REPOSITORIES renovate/renovate
```





<!-- ### Attach IAM Security Policies
Make sure to attach the [AWSCodeCommitFullAccess](https://docs.aws.amazon.com/codecommit/latest/userguide/security-iam-awsmanpol.html#managed-policies-full) policy to your IAM User.  

It is also recommended to attach the [IAMReadOnlyAccess](https://docs.aws.amazon.com/IAM/latest/UserGuide/security-iam-awsmanpol.html) policy to your IAM User.  

**TBC** -->

### (Optional) Set Up Schedule Using Event Rules
In [Amazon EventBridge](https://us-east-1.console.aws.amazon.com/events/home), go to **Events** >> **Rules** and click **Create rule**.  

**TBC**

<hr>
