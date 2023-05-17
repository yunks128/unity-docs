# Unity Release 23.1

## Release Description

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

| Capability                      | Service Area  | Description                                                                                                                                  |
| ------------------------------- | ------------- | -------------------------------------------------------------------------------------------------------------------------------------------- |
| App Pack Generator              | U-ADS         | Given a Jupyter notebook, package it into the format needed for running in Unity ADES (Docker, CWL, stage in, out methods)                   |
| Dockstore API added to Unity Py | U-ADS         | Publish a hosted workflow to Dockstore (e.g. publish workflows, application packages)                                                        |
| Deployment Updates              | U-ADS         | Updates to docker containers, MCP AMIs, and other miscellaneous upgrades for all components                                                  |
| Gitlab Integrations             | U-ADS         | Gitlab project implemented to build other builds, MCP gitlab runners added                                                                   |
| BCDP Appliation Package         | U-AS          | Generic capabilities (regridding) including extended parameters and wrapped as application package for execution in Unity ADES               |
| Jupyter Security                | U-CS          | Added Unity login credentials to Jupyter environment to allow single log in and propagation of token to Unity calls.                         |
| Deployment Venue support        | U-CS          | Github Actions and Control Plane mechanisms for deploying Unity Venues - will lead to self-service service deployment in Unity environments. |
| MCP specific resource tagging   | U-CS          | Documentation for tagging AWS resources in MCP to fulfill cost reporting requirements                                                        |
| Stage-in STAC Download          | U-DS          | Added ability to download files from S3 via STAC                                                                                             |
| Functional Separation           | U-DS          | Separated CMR Search, stage-in, stage-out and catalog steps into discreet steps for future workflow integration                              |
| Initial Implementation          | On-demand SPS | Ability to pre-scale a cluster to reduce processing latency                                                                                  |
| Data Availability Triggers      | On-demand SPS | Respond to data availability                                                                                                                 |
| Shared Ancillary data           | SPS           | Allow sharing of large ancillary files between cluster nodes for scaled processing                                                           |
| Kubernetes Scaling              | SPS           | Enabled kubernetes scaling to support scaled processing and pre-warm capabilities                                                            |
| Automated Testing               | SPS           | Added smoke-tests on deployment to ensure user deployed venues are set up correctly                                                          |
| WPS Parameters                  | SPS           | Enabled generic mapping of WPS inputs to allow for more custom processing                                                                    |
| Unity Landing page              | UIUX          | Unity landing page developed for URL and Unlimited Release (pending)                                                                         |
| Processing libraries            | UIUX          | Added job service encapsulation to Unity-py                                                                                                  |

## System Release Test Plan

System Test plan is in progress and can be viewed at the [Unity System Test Repository](https://github.com/unity-sds/unity-system-test)

## System Test Summary
