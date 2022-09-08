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

Unity is a set of cloud services that provide an end-to-end tool set for science analysis, algorithm development, and scaled processing. They key **** (external facing) services available are:

* **Application Development**: the Algorithm Development Service (ADS), which includes a JupyterHub environment, code source-control and the Application Catalog (implemented using Dockstore).
* **Science Processing Service**: For deploying applications and job-management activities you will interact with the WPS-T API provided by the Science Processing Service (SPS) subsystems.&#x20;
* **Data Services**: For data input and output, you interact with the Data Access and Processing API (DAPA) from the Data Services (DS) subsystem.

Jupyter is the primary graphical interface to the system.&#x20;

{% hint style="info" %}
As of the "R2" release of the system, the interface between Dockstore and the rest of the system is currently limited. It will eventually be possible to directly deploy applications from Dockstore into the scaled compute environment (SPS subsystem). The user interface to do so will evolve with system capabilities.
{% endhint %}

### **Unity Prototype Release 0.2 (R2) Discussion**

See the diagram below for reference to the Unity R2 discussion. Blue boxes indicate graphical user-interfaces. Orange boxes are other service subsystems, with grey dotted-border boxes indicating APIs used to interact with those subsystems.

Initially the two GUIs are&#x20;

1. Jupyter - where the system may be interfaced via the indicated APIs
2. Dockstore - where the Application Packages are stored.

![](https://documents.lucid.app/documents/2eaf0390-bb79-4c4d-af02-e7f64e0914a3/pages/.2F-os\_15SZe?a=6273\&x=5292\&y=661\&w=1141\&h=1342\&store=1\&accept=image%2F\*\&auth=LCA%204ffb33e23d9c8212fca25bf036f885be2e9a068b-ts%3D1659395518)

