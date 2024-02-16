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

1. **Create a repository** that houses code/scripts that do a deployment (e.g. terraform, CloudFormation, and/or shell scripts)
2. **Structure the repository** to have the correct entry point directory (e.g. `terraform-unity`).  There is an option to override the `terraform-unity` directory convention (i.e. in the case where multiple source repos lead to the marketplace deployment).  Technically speaking, your terraform code doesn't have to be co-located with your application code.
3. **Create a release** (zip file of the repository).  This should be put in your Github "Releases", in your repo, that can be referenced by a SHA.
4. Fork the [marketplace repo](https://github.com/unity-sds/unity-marketplace)
5. **Add your application**
   1. Under the `applications` directory, create a directory, and version sub-directory for your application.&#x20;
      1. See examples here:  [https://github.com/unity-sds/unity-marketplace/tree/main/applications](https://github.com/unity-sds/unity-marketplace/tree/main/applications)
   2. Create a metadata.json file for your application.  There is an example [here](https://github.com/unity-sds/unity-marketplace/blob/main/applications/sample-application/0.1/metadata.json).&#x20;
      1. [See notes here, about the schema](unity-marketplace.md#marketplace-metadata.json-specification)
6. **Submit your PR** to the [marketplace repo](https://github.com/unity-sds/unity-marketplace)
7. Ensure the checks pass
   1. (Doesn't exist yet, but in the future, there will be a CI/CD system that checks for validity)
8. Merge the PR
9. Application is available!
   1. The application should now be able to be deployed via the Management Console UI.
10. If you want to create a new version of your application in the Marketplace, you would repeat steps 4-9 above.

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

### `properties.Repository`

More for display purposes from the Marketplace UI.  This can be free-form, and will display.

### `properties.Tags`

description coming soon..

### `properties.Category`

description coming soon..

### `properties.IamRoles`

description coming soon..

### `properties.Package`

Represents the packaged-up deployable unit.  This field can be a pointer to a git SHA or zip file URL of a Github release.  The zip file URL is the preferred approach.  This field is that is git-pulled upon deployment via the Management Console.

### `properties.ManagedDependencies`

description coming soon..

### `properties.Backend`

e.g. "terraform"

### `properties.WorkDir`

This can override the default `terraform-unity` directory (see above)

### `properties.DefaultDeployment`

description coming soon..



