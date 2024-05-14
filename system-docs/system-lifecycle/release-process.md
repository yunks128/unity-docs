# Release Process

### Overview

![Release Process](https://user-images.githubusercontent.com/59702631/157528324-35df60f5-2bf1-444e-9612-46a844b578ea.png)

### Code Release



* [SEMVAR](https://semver.org/) versioning is used **for each repository** (That is, **DO NOT USE PI** as a version)
* **Deploying to unity-test or unity-venue-test** require a release
  * Answers "what are we integrating with" when issues arrise.&#x20;
  * Code is in a [github release](https://docs.github.com/en/repositories/releasing-projects-on-github)
    * CHANGELOG included
    * Release description is accurate and up to date
* Artifacts (binary code executables and/or Docker images) are available
  * Available from Release Page
  * Binaries/containers **shall** include the semantic version of the component
  * Binaries/containers **can optionally** include the commithash of the **main branch**
* Software has been deployed, integrated, and [tested](testing.md) in a development environment.
  * All automated tests are passing
    * Unit Test Code Coverage is at 80%
* Documentation is updated, deployed and accessible (gitbook, redoc, api-based docs, etc)
* Github Issues are updated and in resolved (or closed) state. Issues are included in Release notes.

### System Release

System releases are aggregates of individual component releases. These usually approach some milestone (e.g. a PI end) as a way of marking where we are in the development process.

* Manifest of releases [has been generated](https://github.com/unity-sds/unity-project-management/blob/main/repo-management/latest-release.py) and is available
* Code releases have been deployed to the TEST environment
* System Level Verification and Validation (V\&V) [Test plan](https://github.com/unity-sds/unity-project-management/wiki/Testing) has been created and executed for each release
* V\&V Summary is available
* Manual development tests are explicitly marked in the testplan
* Release Announcement is available on the [release page](../../mdps-overview/releases/)
* Review Materials are archived where applicable
* Release Demo(s) has been Scheduled

### Repositories

* Publicized way of requesting issues - github, service desk, etc for your component
* No official guidance on branch strategy, but&#x20;
  * Main should be **releasable** code at any point
* Manual development tests are described in each repository (test/MANUAL-TESTS.md)
  * Should plan for automating manual test.
* License File included in repository
* [Labels are generated](https://github.com/unity-sds/unity-project-management/blob/main/repo-management/labels.py) for the repository
