---
description: >-
  Steps for deploying the management console in a new project account using
  Cloud Formation
---

# Management Console

## 1. Download Latest Template

Unity-CS maintains a Cloud Formation Template that can be used to deploy the Management console to a project account for the first time. The template is maintained in the GitHub repository [https://github.com/unity-sds/cfn-ps-jpl-unity-sds](https://github.com/unity-sds/cfn-ps-jpl-unity-sds)

{% hint style="info" %}
Currently, the unity-sds/cfn-ps-jpl-unity-sds repository is private. If you do not have read access to the repository, please contact [https://github.com/orgs/unity-sds/teams/unity-leads](https://github.com/orgs/unity-sds/teams/unity-leads) to request read access.
{% endhint %}

The Cloud Formation Template can be found here: [https://github.com/unity-sds/cfn-ps-jpl-unity-sds/blob/main/templates/unity-mc.main.template.yaml](https://github.com/unity-sds/cfn-ps-jpl-unity-sds/blob/main/templates/unity-mc.main.template.yaml). Download this file to your computer to be used in later steps.

## 2. Log in to AWS

There are two main ways to use the Cloud Formation template: using the AWS Management Console or using the AWS Command Line Interface (CLI). Both methods will have the same result but it is important that the role used to login has the correct authorization to deploy the Cloud Formation stack.

### Permissions Required

See [necessary-mcp-roles-policies.md](necessary-mcp-roles-policies.md "mention") for a description of the required permissions

## 3. Deploy the Stack

Deploying the stack is as easy as specifying the cloud formation template and filling out the required parameters. Both the AWS Console and CLI methods are described.

{% hint style="info" %}
Currently, the Cloud Formation template assumes that some infrastructure is already setup so that you can simply pass references to existing AWS resources as the parameters for the template. The required AWS infrastructure is:

* 1 VPC
* 2 Public subnets in different availability zones
* 2 Private subnets in different availability zones
* KeyPairName (Deprecated, will be removed in [https://github.com/unity-sds/cfn-ps-jpl-unity-sds/issues/6](https://github.com/unity-sds/cfn-ps-jpl-unity-sds/issues/6))
* PrivilegedPolicyName: Name of an IAM role used for the permission boundary of the EC2 instance profile
{% endhint %}

{% hint style="info" %}
There are a few non-AWS infrastructure parameters needed for the Cloud Formation template as well:

* GithubToken: A personal access token from GitHub.com that has read access for the repositories in the unity-sds organization
* Venue: Name of the venue (e.g. dev, test, prod) associated with this account/deployment
* Proj: Name of the project
{% endhint %}

### Method 1: AWS Console

Follow the AWS guide for Creating a stack on the AWS CloudFormation console: [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html)

During the **Selecting a stack template** step choose the **Upload a template file** option and upload the template file from [#1.-download-latest-template](management-console.md#1.-download-latest-template "mention")

### Method 2: AWS CLI

Follow the AWS guide for Creating a stack with the AWS CLI: [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-cli-creating-stack.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-cli-creating-stack.html)

{% hint style="info" %}
Use the validate-template command to view the description of parameters of the template [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-validate-template.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-validate-template.html)

For example:

`aws cloudformation validate-template --template-body file://${PATH_TO_TEMPLATE}`


{% endhint %}

For ease of use an example create stack command is provided below.

```bash
aws cloudformation create-stack \
  --stack-name ${STACK_NAME} \
  --template-body file://${PATH_TO_TEMPLATE} \
  --capabilities CAPABILITY_IAM \
  --parameters ParameterKey=VPCID,ParameterValue=${VPCID} \
    ParameterKey=PublicSubnetID1,ParameterValue=${PublicSubnetID1} \
    ParameterKey=PublicSubnetID2,ParameterValue=${PublicSubnetID2} \
    ParameterKey=PrivateSubnetID1,ParameterValue=${PrivateSubnetID1} \
    ParameterKey=PrivateSubnetID2,ParameterValue=${PrivateSubnetID2} \
    ParameterKey=InstanceType,ParameterValue=${InstanceType} \
    ParameterKey=PrivilegedPolicyName,ParameterValue=${PrivilegedPolicyName} \
    ParameterKey=GithubToken,ParameterValue=${GithubToken} \
    ParameterKey=Venue,ParameterValue=${Venue} \
    ParameterKey=Project,ParameterValue=${Project} \
  --tags Key=ServiceArea,Value=U-CS
```

## 4. Verify Stack Creation Status

### Method 1: AWS Console

The AWS Console will report the status as the stack gets created. More details can be found in the AWS documentation [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-view-stack-data-resources.html)

### Method 2: AWS CLI

After submitting a `create-stack` command, it is possible to watch the status of the stack creation with the command:

```
aws cloudformation describe-stack-events --stack-name ${STACK_NAME}
```

More details can be found in the AWS documentation [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-listing-event-history.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/using-cfn-listing-event-history.html)

## Accessing the Management Console

TODO: pending API Gateway, and MC creation of SSM endpoint parameter, getting sorted





