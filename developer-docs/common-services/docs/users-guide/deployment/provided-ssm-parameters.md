---
description: >-
  This page describes the SSM Parameters that CS creates in a project account
  either at account initialization or during a key-resource deployment.
---

# Unity SSM Parameters

## WIP EXAMPLE PARAMETERS- NOT YET IMPLEMENTED

## /account

<table><thead><tr><th width="270.3333333333333">Name</th><th>Description</th><th>Type</th></tr></thead><tbody><tr><td>/account/project</td><td>Name of the project that owns and operates the account. The {project} part of a {project}-{venue} account name.</td><td>String</td></tr><tr><td>/account/venue</td><td>Name of the venue that runs in the account. The {venue} part of a {project}-{venue} account name.</td><td>String</td></tr><tr><td>/account/id</td><td>The AWS account ID.</td><td>String</td></tr></tbody></table>

### /account/network&#x20;

<table><thead><tr><th width="325.3333333333333">Name</th><th>Description</th><th>Type</th></tr></thead><tbody><tr><td>/account/network/subnets/private</td><td>List of private subnets in the account.</td><td>StringList</td></tr><tr><td>/account/network/subnets/public</td><td>List of public subnets in the account.</td><td>StringList</td></tr><tr><td>/account/network/vpc/ids</td><td>List of usable VPCs in the account,</td><td>StringList</td></tr></tbody></table>

### /sps/...

| Name       | Description | Type |
| ---------- | ----------- | ---- |
| /sps/.../a | tbd         | tbb  |
| /sps/.../b | tbd         | tbc  |
|            |             |      |
