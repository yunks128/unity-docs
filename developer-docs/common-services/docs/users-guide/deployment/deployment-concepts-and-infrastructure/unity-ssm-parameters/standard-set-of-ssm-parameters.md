---
description: >-
  This page describes the SSM Parameters that CS and/or the service area
  deployments create in a project account. These can be accessed by other
  service areas, as needed.
---

# Standard Set of SSM Parameters

## WIP EXAMPLE PARAMETERS- NOT YET IMPLEMENTED

## /unity/<mark style="color:blue;">core</mark>/...

<table><thead><tr><th width="360.3333333333333">SSM Key</th><th width="297">Description</th><th>Type</th></tr></thead><tbody><tr><td>/unity/core/aws_account_id</td><td>The AWS account ID.</td><td>String</td></tr><tr><td>/unity/core/project</td><td>Name of the project that owns and operates the account. The {project} part of a {project}-{venue} account name.</td><td>String</td></tr><tr><td>/unity/core/venue</td><td></td><td>String</td></tr></tbody></table>

## /unity/<mark style="color:blue;">account</mark>/...

<table><thead><tr><th width="359.3333333333333">SSM Key</th><th width="294">Description</th><th>Type</th></tr></thead><tbody><tr><td>/unity/account/venue </td><td>Name of the venue that runs in the account. The {venue} part of a {project}-{venue} account name.</td><td>String</td></tr></tbody></table>

## /unity/<mark style="color:blue;">account</mark>/<mark style="color:blue;">network</mark>/...&#x20;

<table><thead><tr><th width="356.3333333333333">Name</th><th width="274">Description</th><th>Type</th></tr></thead><tbody><tr><td>/unity/account/network/subnet_list</td><td></td><td></td></tr><tr><td>/unity/account/network/subnets/private</td><td>List of private subnets in the account.</td><td>StringList</td></tr><tr><td>/unity/account/network/subnets/public</td><td>List of public subnets in the account.</td><td>StringList</td></tr><tr><td>/unity/account/network/vpc/ids</td><td>List of usable VPCs in the account,</td><td>StringList</td></tr><tr><td>/unity/account/network/vpc_id</td><td></td><td></td></tr><tr><td>/unity/account/network/ssl</td><td>The ARN of the SSL certificate for use in the mdps venue.</td><td>String</td></tr></tbody></table>

###

## /unity/<mark style="color:blue;">account</mark>/<mark style="color:blue;">eks</mark>/...



<table><thead><tr><th width="362.3333333333333">SSM Key</th><th width="282">Description</th><th>Type</th></tr></thead><tbody><tr><td>/unity/account/eks/cluster_sg </td><td></td><td></td></tr><tr><td>/unity/account/eks/eks_iam_node_role</td><td></td><td></td></tr><tr><td>/unity/account/eks/eks_iam_role</td><td></td><td></td></tr><tr><td>/unity/account/eks/node_sg</td><td></td><td></td></tr></tbody></table>

###

## /unity/<mark style="color:blue;">account</mark>/<mark style="color:blue;">roles</mark>/...



<table><thead><tr><th width="363.3333333333333">SSM Key</th><th width="276">Description</th><th>Type</th></tr></thead><tbody><tr><td>/unity/account/roles/eksInstanceRoleArn</td><td></td><td></td></tr><tr><td>/unity/account/roles/eksServiceRoleArn</td><td></td><td></td></tr></tbody></table>

## /<mark style="color:blue;">sps</mark>/...

<table><thead><tr><th width="348.3333333333333">Name</th><th width="283">Description</th><th>Type</th></tr></thead><tbody><tr><td><code>/unity/sps/{sps deployment name}/spsApi/url</code></td><td>the full load balancer url, for the SPS API.  For example: <code>/unity/sps/unity-dev-sps-hysds-eks-drew/spsApi/url</code></td><td>String</td></tr><tr><td>/sps/processing/workflows/unity_username</td><td></td><td></td></tr><tr><td>/sps/processing/workflows/unity_password</td><td></td><td></td></tr></tbody></table>



## /<mark style="color:blue;">mcp</mark>/...



| MCP-defined values                                                                                                                                      |
| ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| See: [https://caas.gsfc.nasa.gov/display/GSD1/MCP+AMI+Configuration+and+Usage](https://caas.gsfc.nasa.gov/display/GSD1/MCP+AMI+Configuration+and+Usage) |



##

## /unity/<mark style="color:blue;">cs</mark>/<mark style="color:blue;">routing</mark>/...



| SSM Key                                                   | Description |   |
| --------------------------------------------------------- | ----------- | - |
| /unity/cs/routing/api-gateway/rest-api-id                 |             |   |
| /unity/cs/routing/shared-services-api-gateway/rest-api-id |             |   |
|                                                           |             |   |



## unity/<mark style="color:blue;">cs</mark>/<mark style="color:blue;">security</mark>/...

| SSM Key                                                           | Description | Type |
| ----------------------------------------------------------------- | ----------- | ---- |
| /unity/cs/security/shared-services-cognito-user-pool/user-pool-id |             |      |
|                                                                   |             |      |
|                                                                   |             |      |

## Other Values

| SSM Key                                                                  | Description | Type |
| ------------------------------------------------------------------------ | ----------- | ---- |
| /unity/project-api-gateway/cs-lambda-authorizer-cognito-client-id-list   |             |      |
| /unity/project-api-gateway/cs-lambda-authorizer-cognito-user-pool-id     |             |      |
| /unity/project-api-gateway/cs-lambda-authorizer-cognito-user-groups-list |             |      |
| /unity/project-api-gateway/cs-lambda-authorizer-invoke-role-arn          |             |      |
