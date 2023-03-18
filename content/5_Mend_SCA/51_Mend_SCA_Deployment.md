---
title: "Deployment"
chapter: false
weight: 51
---

## **Deploy Mend SCA on AWS CodeBuild**

### Build Specification

{{% notice info %}}
For syntax reference and more information on build specification (buildspec) files, refer to [Build specification reference for CodeBuild](https://docs.aws.amazon.com/codebuild/latest/userguide/build-spec-ref.html) under AWS CodeBuild User Guide.
{{% /notice %}}

Under the Developer Tools -> CodePipeline you will find the MendWorkshop pipeline that just has been created. 
It's pointing to the AWS CodeCommmit repository that hosts the example application code EasyBuggy and AWS CodeBuild project CI-easybuggy. 

To integrate **Mend SCA** with AWS CodeBuild, you'll need to update your existing build specification (`buildspec.yml`) file, which is placed in the root of your source directory. Go to the easybuggy CodeCommit repository and edit buildspec.yml. If you do not have committer access to the source repo, you can also use the CodeBuild console to insert build commands manually).  


![Update build specification](/images/mend-sca/mend-sca-update-buildspec.png)


### Build Environment
#### **Mandatory Parameters**
Add the following variables and secrets to the buildspec's `env` sections:

| Variable          | Description                                                            |
|:------------------|:-----------------------------------------------------------------------|
| `WS_WSS_URL`      | Your Mend organization server URL, suffixed with the `/agent` endpoint |
| `WS_APIKEY`       | Your Mend organization token (API Key)                                 |
| `WS_USERKEY`      | Your Mend service user token (User Key)                                |
| `WS_PRODUCTTOKEN` | Your Mend product token (Product Token)                                | 

{{< importcode "../static/yaml/app_buildspec.yml" 2 7 "yaml">}}

{{% notice info %}}
Your API Key, User Key and Product Token can be found in the confirmation email you received during the [Setting Up Mend User Account](/2_prerequisites/10_setup_mend_account.html) step.
{{% /notice %}}

For non-Mend customers, use the following URL (`https://saas.mend.io/agent`), otherwise, use the appropriate URL according to your environment. API Key, User Key and Product Token are defined by Stack using [AWS Secret Manager](https://aws.amazon.com/about-aws/whats-new/2019/11/aws-codebuild-adds-support-for-aws-secrets-manager/), which helps you manage credentials. You can find them in Mend Access Keys secret under AWS Secrets Manager -> Secrets. 

Add also required exported-variables:
{{< importcode "../static/yaml/app_buildspec.yml" 9 4 "yaml">}}

#### **Additional Optional Parameters**
Additional environment variables you can add for extended functionality

| Variable            | Description                                                                                                                             |
|:--------------------|:----------------------------------------------------------------------------------------------------------------------------------------|
| `WS_FILESYSTEMSCAN` | Set to `false` to only enable package manager-based dependency resolution, avoiding source language scanning                            |
| `WS_SCANCOMMENT`    | Add a comment to a scan, to be displayed in the Project Vitals panel of the Project pages, as well as the Plugin Request History Report |
| `WS_CHECKPOLICIES`  | Enable policy check during the scan, to allow build failure on policy violation                                                         |


```yaml
env:
  variables:
    {...}
    WS_FILESYSTEMSCAN: false
    WS_SCANCOMMENT: $CODEBUILD_INITIATOR
    WS_CHECKPOLICIES: true
```

### Build Phases
Add the following commands to the `pre_build`, `build` or `post_build` phase to download and execute the [Mend Unified Agent](https://docs.mend.io/bundle/unified_agent/page/getting_started_with_the_unified_agent.html):

{{< importcode "../static/yaml/app_buildspec.yml" 14 30 "yaml">}}

Adding the parameter `on-failure: ABORT` will break the build in case of a scan failure (whether if it's connectivity issue or an actual policy violation, in case any [Policies](https://docs.mend.io/bundle/sca_user_guide/page/managing_automated_policies.html) were configured).  
It is recommended to use that parameter only for builds triggered by pull requests or release branches, but not on CI builds.  

You can also add additional logic to break the build (or trigger other actions) based on the results of the scan, depending on the scan's exit code.  
Refer [here](https://docs.mend.io/bundle/unified_agent/page/exit_codes.html) for more information about the **Mend Unified Agent** exit codes.  

### Commit the changes

That's pretty much it! :)
Once you commit your changes to the source repository, CodePipeline will start a build, since it is configured to trigger CodeBuild once there is a valid commit to the repository. You can find your CodePipeline in the In progress status.<hr>
![Update build specification](/images/mend-sca/mend-sca-codepipeline-in-progress.png)