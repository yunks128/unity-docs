---
description: This page describes naming conventions for SSM Parameters.
---

# SSM Parameter Naming Requirements and Conventions

The only requirements for SSM Parameter key names is that their path start with `/unity/`  followed by a  service area name, where the service area name is one of those accepted by the [AWS Resource Tagging Conventions](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/unity-aws-resource-tagging-conventions#servicearea).  For example:\
`/unity/sps/...`\
`/unity/cs/...`\
`/unity/as/...`

In many cases, these parameter names will become hard-coded across different repos. So, service areas should document the parameters they create and aim to make breaking changes to them as little as possible. The **Name Form** and **Name Parts** sections of this document cover suggestions for naming based on the way other projects have done it in the past.\
\
However a service area chooses to name their parameters, it's important that the parameters provided are well documented and that changes to parameter names or value formats are communicated with the dependent teams.

See the [Unity SSM Parameters](./) page for a list of the actual parameters expected in the Unity system.

### Name Form

There is no hard-and-fast requirement for naming here, but in 99% of the cases, parameters should fall under the following two variants:

1.  **Things that are agnostic of a project/venue:**

    `/unity/WHATEVER/...`\
    Examples:&#x20;

    \- `/unity/account/network/vpc_id` (VPC ID doesn’t change per project/venue)\
    \- `/unity/account/network/subnet_list` (“)  \
    \- `/unity/shared-services/aws/account` (hopefully true that project/venues in an account use same SS account)
2. **Things that are tied to a project/venue:** \
   `/unity/${project}/${venue}/WHATEVER/...` \
   Examples:\
   \- `/unity/sbg/dev/sps/${DEPLOYMENT_NAME}/${DEPLOYMENT_COUNTER}/processing/airflow/ui_url` (sbg=project, and dev=venue, in this example)\
   \- `/unity/${PROJECT_NAME}/${VENUE_NAME}/deployment/status` (status of a particular deployment)

The recommended form for the `WHATEVER` portion mentioned above is:\
`{service area}/{category}/{component}/{resource}`&#x20;





### Name Parts

<table><thead><tr><th width="193.33333333333331">Part</th><th width="170">Description</th><th>Examples</th></tr></thead><tbody><tr><td><code>{service area}</code></td><td>The service area that creates and updates the SSM Parameter.</td><td><code>/unity/sps/...</code><br><code>/unity/cs/...</code><br><code>/unity/as/...</code></td></tr><tr><td><code>{category}</code></td><td>The category of service provided by the service area that this SSM Parameter is related to.</td><td><code>/unity/sps/processing/...</code><br><code>/unity/sps/scaling/...</code><br><code>/unity/cs/routing/...</code></td></tr><tr><td><code>{component}</code></td><td>The specific component of that the SSM Parameter is related to. </td><td><code>/unity/sps/processing/hysds/...</code><br><code>/unity/sps/scaling/auto-scaling-group/...</code><br><code>/unity/cs/routing/api-gateway/...</code></td></tr><tr><td><code>{resource}</code></td><td>The descriptive name of what the SSM Parameter is providing a value for.</td><td><code>/unity/sps/processing/hysds/mozart_url</code><br><code>/unity/sps/scaling/auto-scaling-group/arn</code><br><code>/unity/cs/routing/api-gateway/rest-api-id</code></td></tr></tbody></table>

