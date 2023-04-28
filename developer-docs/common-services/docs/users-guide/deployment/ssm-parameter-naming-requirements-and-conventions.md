---
description: This page describes naming conventions for SSM Parameters.
---

# SSM Parameter Naming Requirements and Conventions

The only requirements for SSM Parameter key names is that their path start with a service area name, where the service area name is one of those accepted by the [AWS Resource Tagging Conventions](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-aws-resource-tagging-conventions#servicearea). For example:\
`/sps/...`\
`/cs/...`\
`/as/...`

The rest of these suggestions are offered in hopes of unifying the Unity SMS Parameters names around a common convention.

### Name Form

We suggest that a key have a name of the form:\
`{service area}/{category}/{component}/{resource}`&#x20;

### Name Parts

| Part             | Description                                                                                 | Examples                                                                                                                                                     |
| ---------------- | ------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `{service area}` | The service area that creates and updates the SSM Parameter.                                | <p><code>/sps/...</code><br><code>/cs/...</code><br><code>/as/...</code></p>                                                                                 |
| `{category}`     | The category of service provided by the service area that this SSM Parameter is related to. | <p><code>/sps/processing/...</code><br><code>/sps/scaling/...</code><br><code>/cs/routing/...</code></p>                                                     |
| `{component}`    | The specific component of that the SSM Parameter is related to.                             | <p><code>/sps/processing/hysds/...</code><br><code>/sps/scaling/auto-scaling-group/...</code><br><code>/cs/routing/api-gateway/...</code></p>                |
| `{resource}`     | The descriptive name of what the SSM Parameter is providing a value for.                    | <p><code>/sps/processing/hysds/mozart_url</code><br><code>/sps/scaling/auto-scaling-group/arn</code><br><code>/cs/routing/api-gateway/rest-api-id</code></p> |

