---
description: >-
  The set of deployable Unity components and services are housed in a
  repository, called the Unity Marketplace.
---

# Unity Marketplace

[https://github.com/unity-sds/unity-marketplace](https://github.com/unity-sds/unity-marketplace)

## Prerequisites

coming soon..

## Procedure for Adding a Component to the Marketplace

1. Create a repository that houses code/scripts that do a deployment (e.g. terraform, CloudFormation, and/or shell scripts)
2. Structure the repository to have the correct entry point directory (e.g. `terraform-unity`)
3. Create a zip file of the repository
4. Fork the [marketplace repo](https://github.com/unity-sds/unity-marketplace)
5. Add your application
   1. Under the `applications` directory, create a directory, and version sub-directory for your application.&#x20;
      1. See examples here:  [https://github.com/unity-sds/unity-marketplace/tree/main/applications](https://github.com/unity-sds/unity-marketplace/tree/main/applications)
   2. Create a metadata.json file for your application
      1. [See notes here, about the schema](unity-marketplace.md#marketplace-metadata.json-specification)
6. Submit your PR
7. Ensure the checks pass
   1. (Doesn't exist yet, but in the future, there will be a CI/CD system that checks for validity)
8. Merge PR
9. Application is available!
   1. The application should now be able to be deployed via the Management Console UI.

\--------------------

## Marketplace metadata.json Specification

### Schema

See the [schema here](https://github.com/unity-sds/unity-marketplace#readme).&#x20;

### Elements

### `properties.Name`

description coming soon..

### `properties.Version`

description coming soon..

### `properties.Channel`

description coming soon..

### `properties.Owner`

description coming soon..

### `properties.Description`

description coming soon..

### `properties.Description`

description coming soon..

### `properties.Repository`

description coming soon..

### `properties.Tags`

description coming soon..

### `properties.Category`

description coming soon..

### `properties.IamRoles`

description coming soon..

### `properties.Package`

description coming soon..

### `properties.ManagedDependencies`

description coming soon..

### `properties.Backend`

description coming soon..

### `properties.DefaultDeployment`

description coming soon..



