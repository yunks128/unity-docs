---
description: >-
  Describes the process of deploying Unity resources to the Shared Services
  account
---

# 🚧 Shared Services Deployment

Currently Shared Services deployment is primarily a manual process.  However, automated & streamlined support is intended to be available at a later date.

##

## Required SSM Parameters in shared environments

While the shared services have free reign over creating required SSM parameters for their own use, there are a number of parameters that are required by venues at deployment time, and they are as follows.

| Parameter Name                        | Value                                       |        |
| ------------------------------------- | ------------------------------------------- | ------ |
| /unity/shared-services/dapa/client-id | Client ID to be used for data services      | Manual |
| /unity/shared-services/dapa/api-url   | The API url for calling unity data services | Manual |
|                                       |                                             |        |

First, create the parameters above using the _advanced_ parameter setting:

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.19.57 AM.png" alt=""><figcaption></figcaption></figure>

To share resources with other accounts, a resource share must be created via resource access manager (RAM). Create a resource share and select the above created parameters:

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.23.00 AM.png" alt=""><figcaption></figcaption></figure>

Add the accounts that require access to this share as "principles"

<figure><img src="../../../../../.gitbook/assets/Screenshot 2024-04-25 at 11.24.58 AM.png" alt=""><figcaption></figcaption></figure>

{% hint style="warning" %}
When a new venue comes online, we will need to update the list of principles to share these parameters with!
{% endhint %}

