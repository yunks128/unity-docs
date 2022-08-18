# Unity Prototype Release 1 Documentation

## Release Description

This is the first release (R1) of the Unity prototype. The items in this release are stand alone capabilities, and will be integrated in an end to end platform in Release 2 and 3 of the unity prototype. All High and Critical objectives were delivered completely or partially during this development timeframe, aside from

## Capabilities by Service Area

| Capability                                         | Service Area | Description                                                                                                                                                                             |
| -------------------------------------------------- | ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Algorithm Catalog                                  | U-ADS        | Ability to submit and view algorithms in a locally deployed CMR.                                                                                                                        |
| Sounder SIPS L1A Algorithm Package                 | U-ADS        | Overview of Applicaiton Package Architecture. Algorithm Packages created for the L1A and L1B processing algorithms                                                                      |
| Sounder SIPS L1A Algorithm Package                 | U-SPS        | Local (non cloud, non-HYSDS) execution of the L1A and L1B using CWL, docker, and data in S3                                                                                             |
| Data Ingest Interface                              | U-DS         | Documentation on triad of responsibilities for data ingest within the Unity system (e.g. ingest data from SPS Product generation)                                                       |
| Sounder SIPS L0 Data in U-DS (January 2016)        | U-DS         | Ability to download and store L0 Sounder SIPS data from the GESDISC Archive within unity system for further processing. Showcases JPL AWS deployment of Cumulus and ingestion workflows |
| OGC APIs                                           | U-SPS        | Example of stubbed implementation of OGC WPS-T standards on top of HySDS. Can deploy algorithsm, request work, and monitor status of a single job or all jobs of a deployed algorithm   |
| HySDS as a managed Service - Kubernetes Deployment | U-SPS        | Example of HySDS deployed and running in a local kubernetes cluster                                                                                                                     |
| Authentication Mechanism                           | U-CS         | Application Security guidelines and authentication mechanism deployed to a JPL AWS API Gateway                                                                                          |
| Build Infrastructure                               | U-CS         | Build using github actions of U-SPS and U-CS components. Artifact management in Google Cloud Repository                                                                                 |
| Deployment Infrastructure                          | U-CS         | Deployment of U-CS artifacts to AWS using github actions, github secrets, and terraform cloud                                                                                           |
| Basic Cost Viewing                                 | U-CS         | Basic (cloud native) cost monitoring in MCP and JPL AWS                                                                                                                                 |
| Cumulus Deployment                                 | U-DS         | Cumulus deployment in JPL AWS **and MCP**. Workflows instantiated and VPC/Internet gateway setup to access restricted GESDISC content. Data ingest mentioned above (January 2016)       |

## Deferred to R2

| Capability                                             | Service Area | Deferral Reason                                |
| ------------------------------------------------------ | ------------ | ---------------------------------------------- |
| Infrastructure Deployment Software for MCP Deployment  | U-ADS        | Delays in MCP Availability                     |
| U-SPS instantiation and execution (prototype)          | U-SPS        | Delays in MCP Availability                     |
| Deployment prototype (includes basic U-SPS deployment) | U-CS         | Coupled with above- Delays in MCP Availability |

## System Release Test Plan

System Test plan is in progress and can be viewed at the [Unity System Test Repository](https://github.com/unity-sds/unity-system-test)

## System Test Summary

## Release Artifacts
