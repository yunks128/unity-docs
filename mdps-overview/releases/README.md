---
description: Information about Unity releases
---

# Releases

### System Releases

A System releases is a snapshot of the deployed system, that is, it is a snapshot of the [system context](https://unity-sds.github.io/unity-architecture/#/HOME) of the [C4 architecture model](https://c4model.com/). A system release is comprised of A) the set of code released to production, B) the verification and validation that has been done at the system level, and C) the documentation and guides for using the software or platform.

Initially, system releases will be manually curated efforts every few months. The long term goal will be to automate 'system releases' and have them be a by-product of the code release and promotion to test and production environments.

| Version             | Release                                                      | Date       | Description                                                  |
| ------------------- | ------------------------------------------------------------ | ---------- | ------------------------------------------------------------ |
| Unity Prototype 1.0 | [Unity Prototype 1.0.0 Release](unity-prototype-1.0.0.md)    | 4/10/2024  | Prototype release for MDPS Validation and SBG-like workflows |
| Unity .1 R1         | [Unity .1 Prototype Release 1](unity-prototype-release-1.md) | 04/15/2022 | Unity Prototype R1...                                        |
| Unity .1 R2         | [Unity .1 Prototype Release 2](unity-prototype-release-2.md) | 07/15/2022 | Unity Prototype R2...                                        |
| Unity .1 R3         | [Unity .1 Prototype Release 3](unity-prototype-release-3.md) | 09/15/2022 | Unity Prototype R3...                                        |
| Unity 23.1          | [Unity 23.1 Prototype Release](unity-release-23.1.md)        | 04/07/2023 | Unity 23.1 Release                                           |
| Unity 23.2          | Unity 23.2 Prototype Release                                 | 06/30/2023 | TBD                                                          |
| Unity 23.3          | Unity 23.3 Prototype Release                                 | 10/31/2023 | Unity 23.3 Release                                           |

Release Process

### Code Releases

Each component of Unity (for example, the algorithm catalog) has its own set of releases. Releases can be viewed from each of the repositories within this organization. The component release happens at the [container](https://c4model.com/#ContainerDiagram) (no, not Docker container) level of the C4 architecture model. The development team is responsible for the code, unit testing, integration testing, and documentation of their component.

### Documentation

**Architecture and Design**

Architecture Diagrams, sequence diagrams, and more are available on our [architecture overview page](https://unity-sds.github.io/unity-architecture/#/).

**Requirements**

**Issue Management**

**Test Plan**

* System Test Plans are stored in JPLs Test Rails.
* System Test Procedures are stored in JPLs Test Rails (soon to be its own repository for automated testing)

**Configuration Management**

**Inspiration**

"\[We've experienced] lots of red tape between teams - which made things difficult. I am proposing we should not do that."
