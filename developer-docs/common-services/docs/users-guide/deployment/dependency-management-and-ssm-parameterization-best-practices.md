---
description: Guidance on how dependencies should be managed through SSM parameterization.
---

# Dependency Management and SSM Parameterization Best Practices

Many applications within Unity have some kind of deployment time  dependency. These dependencies can be on infrastructure brought up by the CS team, on applications deployed by other service areas, or on information about the account itself.&#x20;

## Dependency Overview

Some dependencies can be hard-coded and communicated between teams because they remain constant across accounts. &#x20;

Other dependencies are not constant across accounts and - to prevent managing deployment configurations for every possible account - require some form of parameterization.

**Dependency Example 1 - Application deployment dependent on infrastructure**: \
The SPS team's WPS-T endpoints use Kubernetes services that dynamically provision AWS load balancers. In order to specify which subnet to provision the load balancer into, the SPS team needs to know which subnets are available and good for load balancers. These subnets are identified with a subnet-id that changes between accounts

\
**Dependency Example 2 - Application deployment dependent on account information**: \
A team wants to dynamically create AWS resources as a part of their deployments. These resources need to be tagged according to the [Unity tagging conventions](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-aws-resource-tagging-conventions). In order to form the tags, the team need information about the account it's deploying in such as the project name and venue.

\
**Dependency Example 3 - Application deployment dependent on other application deployment**: \
A team wants to read the content of an S3 bucket that is created programmatically by another team's deployment. The reading team needs to know the name of the S3 bucket setup by the other team. The S3 Bucket name is a good example of a dependency that could remain constant across accounts and be hardcoded by any team looking to use it.

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
