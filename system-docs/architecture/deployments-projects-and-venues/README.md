---
description: >-
  discussions and definitions of the concepts used in deploying the Unity System
  as a functional SDS for a mission.
---

# Deployments- Projects and Venues

<figure><img src="../../../.gitbook/assets/Multi-tenant deployment - Shared and Single Tenant Services (1) (1).png" alt=""><figcaption><p>Multi-tenant (shared) vs Single-tenant services</p></figcaption></figure>

Unity is comprised of shared services and project specific managed services. Shared services are services that are common to all users of the unity system, and they interact with one deployment of those services. These are true multi-tenant systems. Single-Tenant Managed services, on the other hand, are deployed for specific purposes and are isolated from other managed services. single tenant system are only accessible by certain project users.

* Shared Service - A service that is used by all users, across all projects, in the unity system.
* Project - a tenant of the system. Projects could be a mission (e.g. SBD, Mars 2020, etc) or a Research and Analysis (R\&A) project funded by proposal. Projects are a logical grouping of project owned and operated resources.
* Venue - Venues contain deployments of one or more managed services. In practice, these are individual Mission Cloud Platform (MCP) AWS accounts with specific purposes and goals and serve as a cost isolation implementation as well as the way in which we can lifecycle project specific services and adaptations.

<figure><img src="../../../.gitbook/assets/Multi-tenant deployment - PRoject and Venue Deployment.png" alt=""><figcaption><p>Logical Deployment of Unity Projects</p></figcaption></figure>

In the above diagram, there are 3 separate projects using Unity. They will all deploy their own venues and services within their project. Remember, projects are logical groupings. You can't "log in" to a project. All three represented projects will use the same Unity Shared Services, such as authentication, the same data catalog, and so on.

A project cannot use Unity without at least a single venue. A project can consist of more than one venue. The above diagram shows how different projects can use one or more venues to run their missions.

<figure><img src="../../../.gitbook/assets/Multi-tenant deployment - Project and Venue Deployment w venues.png" alt=""><figcaption></figcaption></figure>

Project 1 has created 2 venues.

* Production Venue (PV) - A venue for forward stream product generation as well as historical reprocessing.
* Algorithm Development Venue (ADV) - A venue for the development of algorithms, workflows, and their validations.

The budget for the production venue (PV)  might be much higher because it's doing a lot of scalable processing and data generation, while the algorithm development venue (ADV) may require less cost, but have more users interacting with it and developing algorithms.&#x20;

Algorithms defined in ADV can be "promoted" to PV after significant development and validation. Many projects, such as project 2 in the diagram above, might have a specific venue for validation.

In practice, each venue is its own AWS account in MCP. These allow for specific access restrictions, cost controls, and the ability to lifecycle managed services and project adaptations. With this set up, the algorithm development venue in Project 1 above cannot impact the costs or resources of the Production venue.

Likewise, Project 2 can develop new algorithms, deploy them to their test venue, validate them, and then deploy them to their production venues. The use of venues allows for these 2 use cases.

Within a venue, there can be many different managed services depending on the venue's purpose. The next section will detail these managed services deployed to a venue.





