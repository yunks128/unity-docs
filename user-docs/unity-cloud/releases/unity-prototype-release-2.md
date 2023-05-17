# Unity Prototype Release 2 Documentation

## Release Description

This is the Second release (R2) of the Unity prototype.

## Capabilities by Service Area

| Capability                                         | Service Area | Description                                                            |
| -------------------------------------------------- | ------------ | ---------------------------------------------------------------------- |
| Algorithm Catalog                                  | U-ADS        | Ability to submit and view algorithms in an MCP deployed Dockstore.    |
| Juyter Hub                                         | U-ADS        | Ability to run code from a hosted Jupyter Hub instance within MCP.     |
| Juyter Hub Cognito                                 | U-CS         | Cognito Integration with Jupyter Hub                                   |
| Data Ingest                                        | U-DS         | Ingested L0 data for all of 2016                                       |
| DAPA Endpoints                                     | U-DS         | DAPA Endpoint for listing collections and granules with time filters   |
| DAPA Endpoint Cognito                              | U-DS         | DAPA Endpoint requires cognito authentication                          |
| Data Stage-in/Stage-out                            | U-DS         | SPS used-capability for staging in and staging out data to U-DS        |
| OGC APIs                                           | U-SPS        | Implementation of OGC WPS-T standards on top of HySDS. Deployed to MCP |
| HySDS as a managed Service - Kubernetes Deployment | U-SPS        | Deployed ADES to MCP TEst environment                                  |
| Authentication Mechanism                           | U-CS         | API Gateway deployment and Cognito Authentication on endpoints         |
| Build Infrastructure                               | U-CS         | EKS cluster provisioning capability delivered to MCP                   |
| Sounder SIPS Tutorials                             | UI/UX        | tutorials for SounderSIPS team to exercise R.2 release.                |
| Documentation                                      | All          | initial deployment of https://unity-sds.gitbook.io/docs/               |

## Deferred to R3

| Capability                                    | Service Area | Deferral Reason                                                                         |
| --------------------------------------------- | ------------ | --------------------------------------------------------------------------------------- |
| U-SPS instantiation and execution (prototype) | U-SPS        | Issues getting CWL working in ADES                                                      |
| U-SPS WPS-T full implementation               | U-SPS        |                                                                                         |
| CI/CD system for built applications           | U-ADS        | Complexity in Jupyter hub deployment to MCP environment, focus on dockstore and Jupyter |

## System Release Test Plan

System Test plan is in progress and can be viewed at the [Unity System Test Repository](https://github.com/unity-sds/unity-system-test/releases/tag/0.2.0)

## System Test Summary

## Release Artifacts
