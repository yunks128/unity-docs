---
description: >-
  Describes the process of deploying Unity resources to the Shared Services
  account
---

# 🚧 Shared Services Deployment

Currently Shared Services deployment is primarily a manual process.  However, automated & streamlined support is intended to be available at a later date.

## Required SSM Parameters in shared environments

While the shared services have free reign over creating required SSM parameters for their own use, there are a number of parameters that are required by venues at deployment time, and they are as follows.

| Parameter Name                        | Value                                       |        |
| ------------------------------------- | ------------------------------------------- | ------ |
| /unity/shared-services/dapa/client-id | Client ID to be used for data services      | Manual |
| /unity/shared-services/dapa/api-url   | The API url for calling unity data services | Manual |
|                                       |                                             |        |
