---
description: >-
  The set of deployable Unity components and services are housed in a
  repository, called the Unity Marketplace.
---

# Unity Marketplace

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
6. Submit your PR
7. Ensure the checks pass
8. Merge PR
9. Application is available!

