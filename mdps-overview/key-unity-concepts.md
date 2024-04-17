---
description: From data to applications to jobs to analysis
---

# Key Unity Concepts

The following key concepts will be explored in this guide:

* System Overview
* Development Environment
* Data Catalog
* Application Catalog
* Processing System (to handle job requests)

{% hint style="info" %}
This page is a work-in-progress and will include additional material and links to tutorials, such as a tutorial for 'Publishing your first algorithm'
{% endhint %}

## System Overview

<figure><img src="../.gitbook/assets/ESO Context - Platform Functional Areas (4).png" alt=""><figcaption><p>High level functional areas</p></figcaption></figure>

Unity is a set of managed services that provide an end-to-end tool set for science analysis, algorithm development, and scaled processing. They key (external facing) services available are:

* **Algorithm Development**: the Algorithm Development Service (ADS), which includes a JupyterHub environment, code source-control and the Application Catalog (implemented using Dockstore).
* **Science Processing Services**: For deploying applications and job-management activities you will interact with the WPS-T API provided by the Science Processing Service (SPS) subsystems.&#x20;
* **Data Services**: For data input and output, you interact with the Data Access and Processing API (DAPA) from the Data Services (DS) subsystem.
* **Infrastructure**: Underpinning all other services is a set of deployment, metrics, monitoring and  authentication & authorization tooling and services.

Jupyter (ADS) and Airflow (SPS) are the primary graphical interfaces to the system.&#x20;

{% hint style="info" %}
As of the "prototype 1" release of the system, the interface between Dockstore and the rest of the system is currently limited. It will eventually be possible to directly deploy applications from Dockstore into the scaled compute environment (SPS subsystem). The user interface to do so will evolve with system capabilities.
{% endhint %}

### Key Technologies

Below are the key technologies supported by the MDPS system. These are the technologies that will be supported long term by the MDPS project. Specific implementation technologies can change over time, but we are committing to the following core set of technologies:

**OGC Application Packages**: Based on[ OGC best practices](https://docs.ogc.org/bp/20-089r1.html), application packages are the bundling of executable code and how- that is, the interface- that code is to be run. Here we package executables as containers and the interface is implemented as common workflow language documents. Application Packages are the standard unit of execution, and support many languages, options, and dynamic inputs.

**Containers**:  a package of code and its dependencies based on the [https://opencontainers.org/](https://opencontainers.org/) specification. Often "Docker" is the first thought when container are mentioned, but MDPS strives to be agnostic on the _runner_ (Docker, podman, and others) of the container.

**CWL** ([Common Workflow Language](https://www.commonwl.org/)): a standard way of calling an executable (e.g. command line or docker container) with defined inputs and outputs.&#x20;

**DAPA (Data Access and Processing API)**: a recommended way to searching and retrieve data products or transformation and basic analysis on the data products. For example, a subset of data or an area-aggregated time series can be requested.

**STAC** ([SpatioTemporal Asset Catalogs](https://stacspec.org/en))**:** a common way to define geospatial records which can include metadata and one or more files (referred to as assets). STAC is used to define inputs and outputs of a process. These can be remote to the MDPS system, within the MDPS system, or even local to the process itself.

**WPS/WPS-T**: Web Processing Service and Web Processing Service-Transactional are OGC standards for the deployment, execution, and monitoring of a process. We use WPS to standardize how an application package is deployed, executed, and results are returned.

<figure><img src="../.gitbook/assets/ESO Context - Platform Functional Areas and Standards (2).png" alt=""><figcaption><p>Functional Areas with Key Technologies</p></figcaption></figure>

