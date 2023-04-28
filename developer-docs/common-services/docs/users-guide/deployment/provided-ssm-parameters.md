---
description: >-
  This page describes the SSM Parameters that CS creates in a project account
  either at account initialization or during a key-resource deployment.
---

# Provided SSM Parameters

## WIP EXAMPLE PARAMETERS- NOT YET IMPLEMENTED

## /account

| Name             | Description                                                                                                     | Type   |
| ---------------- | --------------------------------------------------------------------------------------------------------------- | ------ |
| /account/project | Name of the project that owns and operates the account. The {project} part of a {project}-{venue} account name. | String |
| /account/venue   | Name of the venue that runs in the account. The {venue} part of a {project}-{venue} account name.               | String |
| /account/id      | The AWS account ID.                                                                                             | String |

### /account/network&#x20;

| Name                             | Description                             | Type       |
| -------------------------------- | --------------------------------------- | ---------- |
| /account/network/subnets/private | List of private subnets in the account. | StringList |
| /account/network/subnets/public  | List of public subnets in the account.  | StringList |
| /account/network/vpc/ids         | List of usable VPCs in the account,     | StringList |

