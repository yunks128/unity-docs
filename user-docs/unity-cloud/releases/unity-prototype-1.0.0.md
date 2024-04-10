# Unity Prototype 1.0.0

## Release Description

Prototype release 1.0.0 of the Unity system brought updates and quality of life improvements to many parts of the system. Includes the implementation of SBG-like workflows via SISTER emit data production algorithms. This release also includes the SPS airflow implementation, STAC browser, and automated package generation.



## Release Artifacts

Please see the Service Area Changelogs for detailed information and release artifacts.

| Service Area                | Changelog                                                                                                                                                                                                        |
| --------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Algorithm Development       | https://github.com/unity-sds/unity-ads/blob/main/CHANGELOG.md                                                                                                                                                    |
| Analytics Services          | https://github.com/unity-sds/unity-analytics/blob/main/CHANGELOG.md                                                                                                                                              |
| Common Services             | https://github.com/unity-sds/unity-cs/blob/main/CHANGELOG.md                                                                                                                                                     |
| Data Services               | [https://github.com/unity-sds/unity-data-services/blob/main/CHANGELOG.md#unity-release-241---2024-04-09](https://github.com/unity-sds/unity-data-services/blob/main/CHANGELOG.md#unity-release-241---2024-04-09) |
| Science Processing Services | [https://github.com/unity-sds/unity-sps/blob/main/CHANGELOG.md](https://github.com/unity-sds/unity-sps/blob/main/CHANGELOG.md)                                                                                   |
| UIUX                        | https://github.com/unity-sds/ui-ux/blob/main/CHANGELOG.md                                                                                                                                                        |

## Capabilities by Service Area

| Capability             | Service Area | Description                                                                                                                                                                         |
| ---------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| STAC Browser           | U-DS         | Visually browse the data catalog and view product metadata                                                                                                                          |
| Custom Metadata        | U-DS         | Project Metadata registration and search                                                                                                                                            |
| STAC Url Staging       | U-DS         | Ability to stage in via a STAC url in application packages                                                                                                                          |
| Airflow                | U-SPS        | Implementation of Airflow as a UI/EMS+ADES for Unity system. Allows for project defined DAG authoring, monitoring and metrics on executions, and log visibility to identify issues. |
| App-pacakge-generation | U-ADS        | Application package generation service for converting a github repository into an application package for execution in the processing services.                                     |
| Venue Deployments      | U-CS, all    | Dev, test and prod shared service venues deployed. unity-dev, unity-test and unity-prod project venues deployed. SBG-dev venue created.                                             |
| Management Console     | U-CS         |                                                                                                                                                                                     |
| DAPA Process Mapper    | U-AS         |                                                                                                                                                                                     |
| SDAP deployment        | U-AS         |                                                                                                                                                                                     |
| Navbar integration     | UIUX         |                                                                                                                                                                                     |
| unity-py updates       | UIUX         |                                                                                                                                                                                     |
| SBG Workflows          | PSE          |                                                                                                                                                                                     |

## System Release Test Plan

System Test plan is in progress and can be viewed at the [Unity System Test Repository](https://github.com/unity-sds/unity-system-test)

## System Test Summary
