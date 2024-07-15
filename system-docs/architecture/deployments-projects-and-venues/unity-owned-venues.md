---
description: These are the currently "unity" owned venues.
---

# Unity Owned Venues

<div data-full-width="true">

<figure><img src="../../../.gitbook/assets/Unity Venues and SDLC (1).png" alt=""><figcaption></figcaption></figure>

</div>

{% hint style="info" %}
The below environments, while useful for certain cases, are almost entirely moot from an MDPS user perspective. The users venues, weither they be for development, testing, operations, reprocessing, or on-demand usage will all be "production" venues- that is, they live in the "production" MDPS system and point to services in the `Unity-Prod` accounts.
{% endhint %}

### Environments

| Account Name  | Description                                                                                                              | Services                                                                                                                                                                            |
| ------------- | ------------------------------------------------------------------------------------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Unity-Dev     | Development Environment for Unity/MDPS **shared services** development and sandbox activities.                           | <p></p><ul><li>Data Catalog</li><li>App-pack-generation</li><li>Application Catalog</li><li>Web gateway/HTTPD</li><li>API Gateway</li><li>Cognito</li><li>DAPA translator</li></ul> |
| Unity-Test    | Test envrionment for Unity/MDPS **shared service** releases. Load test, nightly testing, end to end system testing, etc. | <p></p><ul><li>Data Catalog</li><li>App-pack-generation</li><li>Application Catalog</li><li>Web gateway/HTTPD</li><li>API Gateway</li><li>Cognito</li><li>DAPA translator</li></ul> |
| Unity-Prod    | Production environment for user facing **shared services**. 99% of users will be active in this environment.             | <p></p><ul><li>Data Catalog</li><li>App-pack-generation</li><li>Application Catalog</li><li>Web gateway/HTTPD</li><li>API Gateway</li><li>Cognito</li><li>DAPA translator</li></ul> |

### Venues

| Venue                                                  | Description                                                                                                    | Services                                                                                                                                                         | Shared Service Environment            |
| ------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------- |
| Unity-Sips-Test (Deprecated / Retired)                 | Venue for SounderSIPS users developing and executing  the chirp, L1a and L1B workflows.                        | <ul><li>ADES (HySDS)</li><li>Job Database</li><li>JupyterHub</li><li>Mission Store</li><li>Dashboard</li></ul>                                                   | Unity-Test (Unity-Prod in the future) |
| Unity-CM                                               | Nightly test venue.                                                                                            | Management Console                                                                                                                                               | Unity-Test                            |
| Unity-Venue-Dev                                        | Development environment for venue deployed services                                                            | <ul><li>ADES (HySDS)</li><li>SDAP</li><li>EMS and ADES (Airflow)</li><li>JupyterHub</li><li>Mission Store</li><li>Dashboard</li><li>Management Console</li></ul> | Unity-Dev                             |
| Unity-Venue-Test                                       | Test environment for venue deployed services                                                                   | <ul><li>EMS and ADES (Airflow)</li><li>JupyterHub</li><li>Mission Store</li><li>Dashboard</li><li>Management Console</li></ul>                                   | Unity-Test                            |
| Unity-Venue-Ops                                        | Operational environment for venue deployed services. Used primarily as a smoke test or final validation venue. | <ul><li>EMS and ADES (Airflow)</li><li>JupyterHub</li><li>Mission Store</li><li>Dashboard</li><li>Management Console</li></ul>                                   | Unity-Prod                            |
| Unity-ASIPS-Int (Running in mcp accoint Unity-SBG-Dev) | Venue to support Atmospheric SIPS Integration use cases                                                        | <ul><li>EMS and ADES (Airflow)</li><li>Mission Store</li><li>Management Console</li><li><em>JupyterHub</em> </li></ul>                                           | Unity-Prod                            |
| Unity-Emit-Dev                                         | Venue to support EMIT Development use cases                                                                    | <p></p><ul><li>EMS and ADES (Airflow)</li><li>Mission Store</li><li>Management Console</li><li><em>JupyterHub</em> </li></ul>                                    | Unity-Prod                            |
