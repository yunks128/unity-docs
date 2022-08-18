# Release Process

### Overview

![Release Process](https://user-images.githubusercontent.com/59702631/157528324-35df60f5-2bf1-444e-9612-46a844b578ea.png)

### Code Release

* Code is in a [github release](https://docs.github.com/en/repositories/releasing-projects-on-github)
  * CHANGELOG included
  * Release description is accurate and up to date
  * License File included in repository
* Publicized way of requesting issues - github, service desk, etc for your component
* Artifacts (binary code executables and/or Docker images) are available
  * Available from Release Page
* Software has been deployed, integrated, and [tested](https://github.com/unity-sds/unity-project-management/wiki/Testing) in a development environment.
* All automated tests are passing
  * Unit Test Code Coverage is at 80%
* Manual development tests are described in each repository (test/MANUAL-TESTS.md)
* For each manual test, an issue has been created to automate said test.
* Documentation is deployed and accessible
  * Up-to-date with release
* Github Issues are updated and in resolved (or closed) state. Issues are included in Release notes.

### System Release

* Delivery Documentation is available for each code release.
* Code releases have been deployed to the TEST environment
* System Level Verification and Validation (V\&V) [Test plan](https://github.com/unity-sds/unity-project-management/wiki/Testing) has been created and executed for each release
* V\&V Summary is available
* Manual development tests are explicitly marked in the testplan
* For each manual test, an issue has been created to automate said test.
* Release Announcement is available
* Review Materials are archived where applicable
* Release Demo(s) has been Scheduled
