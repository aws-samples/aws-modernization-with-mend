---
title: "Deployment"
chapter: false
weight: 51
---

## Deploy Mend SCA on AWS CodeBuild

### Build Specification

{{% notice info %}}
For syntax reference and more information on build specification (buildspec) files, refer to [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) under AWS CodeBuild User Guide.
{{% /notice %}}

To integrate Mend SCA with AWS CodeBuild, you'll need to update your existing build specification (`buildspec.yml`) file.  

If the file doesn't exist, create it under your project's root directory (if you do not have committer access to the source repo, you can also use the CodeBuild console to insert build commands manually).  

![Update build specification](/images/mend-sca/mend-sca-update-buildspec.png)

#### Build Environment
##### Mandatory Parameters
Add the following variables to the buildspec's `env` section:  
<!-- ToDo - replace these hard-coded snippets with the `importcode` shortcode (like in "content/2_Prerequisites/30_self_paced/15_explore_AWS_account.md", line 17) -->
| Variable | Description |
|:----|:----|
| `WS_WSS_URL` | Your Mend organization server URL, suffixed with the `/agent` endpoint |
| `WS_APIKEY` | Your Mend organization token (API Key) |
| `WS_USERKEY` | Your Mend service user token (User Key) |

{{% notice info %}}
Both your API Key and User Key can be found in the confirmation email you received during the [Setting Up Mend User Account](/2_prerequisites/10_setup_mend_account.html) step.
{{% /notice %}}

To avoid hard-coding your Mend API Key and User Key, it is recommended to utilize the [AWS Secret Manager](https://aws.amazon.com/about-aws/whats-new/2019/11/aws-codebuild-adds-support-for-aws-secrets-manager/):  

```yaml
env:
  shell: bash
  variables:
    WS_WSS_URL: "https://saas.mend.io/agent"
  secrets-manager:
    WS_APIKEY: "MendApiKey:Value"
    WS_USERKEY: "MendUserKey:Value"
```

##### Additional Optional Parameters
Additional environment variables you can add for extended functionality

| Variable | Description                                                                                                                               |
|:----|:------------------------------------------------------------------------------------------------------------------------------------------|
| `WS_FILESYSTEMSCAN` | Set to `false` to only enable package manager-based dependency resolution, avoiding source language scanning                              |
| `WS_SCANCOMMENT` | Add a comment to a scan, to be displayed in the Project Vitals panel of the Project pages, as well as the Plugin Request History Report |
| `WS_CHECKPOLICIES` | Enable policy check during the scan, to allow build failure on policy violation                                                           |

```yaml
env:
  variables:
    {...}
    WS_FILESYSTEMSCAN: false
    WS_SCANCOMMENT: $CODEBUILD_INITIATOR
    WS_CHECKPOLICIES: true
```

#### Build Phases
Add the following commands to the `build` or `post_build` phase to download and execute the [Mend Unified Agent](https://docs.mend.io/bundle/unified_agent/page/getting_started_with_the_unified_agent.html):

```yaml
phases:
  build:
    on-failure: ABORT
    commands:
      - export WS_PRODUCTNAME=AWS_$AWS_REGION-$CODEBUILD_INITIATOR
      - export WS_PROJECTNAME=$CODEBUILD_BUILD_ID
      - curl -LJO https://unified-agent.s3.amazonaws.com/wss-unified-agent.jar
      - java -jar wss-unified-agent.jar
```

Adding the parameter `on-failure: ABORT` will break the build in case of a scan failure (whether if it's connectivity issue or an actual policy violation, in case any [Policies](https://docs.mend.io/bundle/sca_user_guide/page/managing_automated_policies.html) were configured).  
It is recommended to use that parameter only for builds triggered by pull requests or release branches, but not on CI builds.  

You can also add additional logic to break the build (or trigger other actions) based on the results of the scan, depending on the scan's exit code.  
Refer [here](https://docs.mend.io/bundle/unified_agent/page/exit_codes.html) for more information about the Mend Unified Agent exit codes.  

<hr>
