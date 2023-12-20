---
description: >-
  An overview of automated deployments and dependency management through SSM
  parameterization.
---

# Deployments, Dependency, and SSM Parameters

Many applications within Unity have some kind of deployment or runtime dependency on other resources in the AWS project account. These dependencies can be on infrastructure brought up by the CS team, on applications deployed by other service areas, or on information about the account itself.&#x20;

## Dependency Examples

Below are a few examples of dependencies that exist in Unity.

**Dependency Example 1 - Application deployment dependent on core resource**: \
The SPS team's WPS-T endpoints use Kubernetes services that dynamically provision AWS load balancers. In order to specify which subnet to provision the load balancer into, the SPS team needs to know which subnets are available and good for load balancers. These subnets are identified with a subnet-id that changes between accounts

\
**Dependency Example 2 - Application deployment dependent on account information**: \
A team wants to dynamically create AWS resources as a part of their deployments. These resources need to be tagged according to the [Unity tagging conventions](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-aws-resource-tagging-conventions). In order to form the tags, the team need information about the account it's deploying in such as the project name and venue.

\
**Dependency Example 3 - Application deployment dependent on other application deployment**: \
A team wants to read the content of an S3 bucket that is created programmatically by another team's deployment. The reading team needs to know the name of the S3 bucket setup by the other team.

## Deployments

During the development of Unity, many of these dependencies have been managed manually (i.e. one team communicates to another about the name of a resource, the state of a deployment, etc.). Long term, Unity is meant to be deployable to an AWS account at the push of a button. Thus rises the need for managing dependencies without a human-in-the-loop. There are three high-level steps to an automated deployment:\
\


1\. Launching the Management Dashboard\
2\. Account setup \
3\. Core resource deployments\
4\. Unity service and apps deployments

<figure><img src="../../../../../../../.gitbook/assets/Screenshot 2023-05-09 at 5.36.09 PM.png" alt=""><figcaption></figcaption></figure>

#### Account Setup

Account setup is the configuration of things that the automated deployment itself is dependent on. This may require some manual steps in some cases.\
\
ex. subnets, VPC, Control Plane

#### Core resources

Core resources are infrastructure or applications that services and apps need, but for one reason or another don't stand up on their own during their deployment. These could be shared resources, or resources that remain relatively static across releases like compute resources.\
\
ex. API Gateway, EKS clusters

#### Services and apps

Services and apps are the resources deployed by a service area. They are deployed and managed using terraform. Services and apps often have dependencies on core resources, account information, or each other.

ex. SPS ADES, On-Demand API

## Dependency Management with AWS's SSM Parameter Store and Terraform

AWS has a great solution for this exact problem called [SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) - it's a straightforward to use per-account, key-value store designed with configuration management in mind.

Terraform makes it easy to create and write to an SSM Parameter Store with [aws\_ssm\_parameter resources](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/ssm\_parameter) and allows users to treat SSM Parameters as data sources with [aws\_ssm\_parameter data sources](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/data-sources/ssm\_parameter).

#### Terraform solution for Dependency Example 1

The CS team below sets the account information parameters using the terraform below.

{% code title="cs_account_info_ssm.tf" %}
```
resource "aws_ssm_parameter" "private_subnets" {
  name  = "/account/networking/subnets/private"
  type  = "StringList"
  value = "subnet-059bc4f467275b59d,subnet-0e4ff7f670ebb4cc3"
}
```
{% endcode %}

When it's time for SPS to deploy their kubernetes services, the SSM parameter with the available parameters is available for use.

{% code title="sps_kube_service.tf" %}
```
data "aws_ssm_parameter" "private_subnets" {
  name = "/account/networking/subnets/private"
}

resource "kubernetes_service" "my_service" {
  ...
  subnets = data.private_subnets.value
  ...
}
```
{% endcode %}

## Deployment Automation with Parameterization

With each part of the deployment managing it's dependencies through SSM the whole picture looks like this:

<figure><img src="../../../../../../../.gitbook/assets/SSM &#x26; Deployments Overview (8).png" alt=""><figcaption></figcaption></figure>
