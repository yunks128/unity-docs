---
description: This page describes naming conventions for SSM Parameters.
---

# SSM Parameter Naming Requirements and Conventions

The only requirements for SSM Parameter key names is that their path start with a service area name, where the service area name is one of those accepted by the [AWS Resource Tagging Conventions](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-aws-resource-tagging-conventions#servicearea). For example:\
`/sps/...`\
`/cs/...`\
`/as/...`

In many cases, these parameter names will become hard-coded across different repos. So, service areas should document the parameters they create and aim to make breaking changes to them as little as possible. The **Name Form** and **Name Parts** sections of this document cover suggestions for naming based on the way other projects have done it in the past.\
\
However a service area chooses to name their parameters, it's important that the parameters provided are well documented and that changes to parameter names or value formats are communicated with the dependent teams.

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

