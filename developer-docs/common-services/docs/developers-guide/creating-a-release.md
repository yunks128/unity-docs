---
description: Procedure detailing how to create a U-CS release
---

# Creating a Release

Procedure:

For each of these repositories:

* [https://github.com/unity-sds/unity-cs-infra](https://github.com/unity-sds/unity-cs-infra)
* [https://github.com/unity-sds/unity-cs-deployment-catalog](https://github.com/unity-sds/unity-cs-deployment-catalog)
* [https://github.com/unity-sds/unity-cs-terraform-transformer](https://github.com/unity-sds/unity-cs-terraform-transformer)
* [https://github.com/unity-sds/unity-cs-security](https://github.com/unity-sds/unity-cs-security)
* TODO:  Are there any new repositories, other than the above, to include this release?

do the following:

1. From the github repo root page, click "Create a new release"
2. Create a new tag, for example "unity-prototype-0.2"
3. Enter a title, for example "Unity Prototype 0.2"
4. Click on "Generate release notes"
5. Manually curate the content, to make human-readable entries.  If possible, create these main sections:
   1. "Bug Fixes"
   2. "Improvements"
   3. "Full Changelog" (this link should already be automatically generate from the "Generate release notes" button click)

Then, back on the main [unity-cs CHANGELOG.md file](https://github.com/unity-sds/unity-cs/blob/main/CHANGELOG.md), add a release section, that reference the releases you created in the above steps.
