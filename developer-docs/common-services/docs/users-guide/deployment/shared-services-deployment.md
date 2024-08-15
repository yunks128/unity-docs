---
description: >-
  Describes the process of deploying Unity resources to the Shared Services
  account
---

# 🚧 Shared Services Deployment

Currently Shared Services deployment is primarily a manual process.  However, automated & streamlined support is intended to be available at a later date.

Nominally, three Shared Services accounts exist:

<figure><img src="../../../../../.gitbook/assets/Unity Venues and SDLC (6).png" alt=""><figcaption><p>the three main MDPS Shared Services accounts</p></figcaption></figure>

Each project account brings their own AWS account, that will be associated with a Unity Shared Services account.  Almost all customers will want to use the `Unity-Prod` account, since this is the most stable Shared Services account.

## Users & Roles Setup

Project users and roles will be defined in the Shared Services account, using AWS Cognito.

In the Shared Services account, the <mark style="color:purple;">**MDPS Team**</mark> needs to create a set of **Cognito user groups** (roles):

* `Unity_Viewer`
* `Unity_Admin`
* NOTE: The Cognito user group naming conventions can be viewed in [Cognito User Group Standards](../security/cognito-user-group-standards.md)

{% hint style="info" %}
Users will NOT be created at the time the Shared Services account is setup, but rather they be created by the <mark style="color:purple;">**MDPS Team**</mark> for each project as part of the [Project Onboarding Procedure](https://unity-sds.gitbook.io/docs/mdps-overview/project-onboarding-procedure).
{% endhint %}

## &#x20;

## Networking Setup

Shared Services deployments have specific network setups to enable the appropriate routing of requests to the desired resource. These resources can live in any number of shared service environments (dev, test, and production) or can live in a venue, which is associated with a single shared service environment.

see [shared-service-network-configurations](shared-services-deployment/shared-service-network-configurations/ "mention") for more details.

## Required SSM Parameters in shared environments

While the shared services have free reign over creating required SSM parameters for their own use, there are a number of parameters that are required by venues at deployment time, and they are as follows.

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-07-03 at 9.43.49 AM.png" alt=""><figcaption><p>Figure 1: screenshot showing shared services SSM parameters</p></figcaption></figure>

| Parameter Name                                                                    | Value                                                            |                                        |
| --------------------------------------------------------------------------------- | ---------------------------------------------------------------- | -------------------------------------- |
| /unity/shared-services/dapa/client-id                                             | Client ID to be used for data services                           | Manual                                 |
| /unity/shared-services/dapa/api-url                                               | The API url for calling unity data services                      | Manual                                 |
| /unity/healthCheck/shared-services/data-catalog                                   | The endpoint used to check the health status of the Data Catalog | Manual                                 |
| /unity/shared-services/cognito/domain                                             | The Cognito domain URL                                           | TBD                                    |
| /unity/shared-services/cloudfront/distribution                                    |                                                                  | TBD                                    |
| /unity/shared-services/data-catalong/deployment/prefix                            |                                                                  |                                        |
| /unity/shared-services/cognito/monitoring-username                                |                                                                  |                                        |
| /unity/shared-services/cognito/monitoring-password                                |                                                                  |                                        |
| /unity/healthCheck/shared-services/process-mapper/url                             |                                                                  |                                        |
| /unity/cs/routing/venue-api-gateway/cs-lambda-authorizer-cognito-user-pool-id     |                                                                  | we should rename this to be consistent |
| /unity/cs/routing/venue-api-gateway/cs-lambda-authorizer-cognito-user-groups-list |                                                                  | we should rename this to be consistent |

First, create the parameters above using the _advanced_ parameter setting:

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.19.57 AM.png" alt=""><figcaption></figcaption></figure>

To share resources with other accounts, a resource share must be created via resource access manager (RAM). Create a resource share and select the above created parameters:

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.23.00 AM.png" alt=""><figcaption></figcaption></figure>

Add the accounts that require access to this share as "principles"

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.24.58 AM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
When a new venue comes online, we will need to update the list of principles to share these parameters with!
{% endhint %}

## Adding a new venue account to a resource share

Modify the "unity-shared-service-resources" share

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.33.21 AM.png" alt=""><figcaption></figcaption></figure>

Add the new principle (AWS Account) to the share:

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.33.34 AM.png" alt=""><figcaption></figcaption></figure>

Save the resource and the new account should now have access.



## Example of accessing a shared SSM value from a Venue account

```
// Get the account ID of shared services
data "aws_ssm_parameter" "ssAcctNum" {
  name = "/unity/shared-services/aws/account"
}

// access a shared parameter
data "aws_ssm_parameter" "cognito_domain" {
  name = "arn:aws:ssm:us-west-2:${data.aws_ssm_parameter.ssAcctNum}:parameter/unity/shared-services/cognito/domain"
}
```

### Command-line Example:

```
// View what the shared params are
$ aws ssm describe-parameters --shared

// Access a specific shared SSM param
$ aws ssm get-parameter --name "arn:aws:ssm:us-west-2:237868123451:parameter/unity/test/cross-account-sharing"
```
