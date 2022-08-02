---
description: Glossary of terminology used in the Unity SDS
---

# Unity SDS Glossary

Algorithm - the process by which data are manipulated. In the unity system, Algorithms are code or  command line utilities that do the data processing.

Application - Essentially the algorithm wrapped in a docker container that can be called as part of the application package.

Application Catalog - The application catalog is the versioned repository of application packages. This is where new and existing application packages can be stored for use in the Unity system.

Application Deployment and Execution Service (ADES) - the processing environment to which  application packages are deployed and executed on. For example, an application for transforming L0 data into L1 can be deployed to an ADES, and then executed with selected L0 data to generate L1 data.

Application Package - The code and Application Descriptor, written in Common Workflow Language, that standardizes and describes the inputs, outputs, and how to call an application.

Job - A specific execution of a deployed application package along with data it needs to generate its output products.

Process - the OGC term for a deployed executable. In unity, "Deployed Executables" are standardized in the Application Package format. Executing a process results in a Job (with status, management, and results).

Workflow - A collection of application packages, configured to work together (chained, scaled, in parallel, etc)&#x20;

Workspace -&#x20;

OGC - [Open Geospatial Consortium](https://www.ogc.org/).&#x20;

SPS - Science Processing Services

ADS - Algorithm Development Services

ADE - Algorithm Development Environment

DS - Data Services

AS - Analysis Services

DAPA - Data Access and Processing API. The API for querying data within the Unity system and requesting transformations on that data.

STAC - SpatioTemporal Access Catalog. [The STAC specification is a **common language to describe geospatial information**, so it can more easily be worked with, indexed, and discovered](https://stacspec.org/). STAC is the result format for Data Service queries.

WPS (WPS-T) - Web Processing Service-Transactional. WPS is an OGC standard interface for requesting the execution, status, management, and results of web processes "jobs". The Transcational (WPS-T) version also includes the deployment and undeployment of processes (e.g. Applicaiton Packages) from a web processing system (e.g. an ADES).&#x20;

Dockstore - An OGC Application Package Catalog that specialized in CWL based workflows.
