# Unity Release 23.2

## Release Description

Release 23.2 of the Unity system brought the workflow components together and  introduced the Unity Management Console and Market place.



## Release Artifacts

Please see the Service Area Changelogs for detailed information and release artifacts.

| Service Area                | Changelog                                                               |
| --------------------------- | ----------------------------------------------------------------------- |
| Algorithm Development       | https://github.com/unity-sds/unity-ads/blob/main/CHANGELOG.md           |
| Analytics Services          | https://github.com/unity-sds/unity-analytics/blob/main/CHANGELOG.md     |
| Common Services             | https://github.com/unity-sds/unity-cs/blob/main/CHANGELOG.md            |
| Data Services               | https://github.com/unity-sds/unity-data-services/blob/main/CHANGELOG.md |
| On-Demand SDS               | https://github.com/unity-sds/unity-on-demand/blob/main/CHANGELOG.md     |
| Science Processing Services | https://github.com/unity-sds/unity-sps/blob/main/CHANGELOG.md           |
| UIUX                        | https://github.com/unity-sds/ui-ux/blob/main/CHANGELOG.md               |

## Capabilities by Service Area

| Capability                          | Service Area | Description                                                                                                               |
| ----------------------------------- | ------------ | ------------------------------------------------------------------------------------------------------------------------- |
| Jobs Database                       | SPS          | SNS Based system for sending job updates from the ADES to the EMS.                                                        |
| CHIRP Workflow Executions           | SPS, ADS, DS | Workflows for chirp data execution including search, stage-in, execution, stage-out and data catalog were developed.      |
| App-Pack-Gen                        | ADS          | Application package generation was developed and updated for STAC based inputs, and automated generation from MCP GitLab. |
| Container Registry                  | ADS          | A private container registry to house non-public containers.                                                              |
| Application Catalog                 | ADS          | Various updates, database restoration, API for retrieving files.                                                          |
| DAPA Timeseries                     | AS           | Prototyped DAPA endpoint and translator for exposing data specific analysis services                                      |
| Nightly Deployment                  | CS           | deploy and teardown on nightly basis including management console, API Gateway, etc.                                      |
| Management Console                  | CS           | Venue setup and deployment scripts                                                                                        |
| Venue Cost Monitoring               | CS           | Documented tagging expectations and query for non-tagged resources.                                                       |
| CMR and staging workflow components | DS           | Added STAC based CMR/DAPA query and stage-in, out tasks for use.                                                          |
| On-demand cloud formation templates | On-Demand    | Simple AWS native way of deploying management console.                                                                    |
| Tophat and dashboard prototype      | UIUX         | Prototype for viewing and submitting jobs to the science processing platform.                                             |

## System Release Test Plan

System Test plan is in progress and can be viewed at the [Unity System Test Repository](https://github.com/unity-sds/unity-system-test)

## System Test Summary
