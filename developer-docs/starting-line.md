---
description: Unity Prototype Release 2 Links to Get Started
---

# Starting Line

MCP access request: to Ramesh

Jupyter notebook home: (dev) [https://jupyter-test-alb-1374844384.us-west-2.elb.amazonaws.com:8000/](https://jupyter-test-alb-1374844384.us-west-2.elb.amazonaws.com:8000/)

Dockstore workflow list (Application Catalog): [http://ec2-52-25-78-158.us-west-2.compute.amazonaws.com:9998/workflows](http://ec2-52-25-78-158.us-west-2.compute.amazonaws.com:9998/workflows)

Dockstore user guide: [https://docs.dockstore.org/en/stable/getting-started/getting-started.html](https://docs.dockstore.org/en/stable/getting-started/getting-started.html)

Learning Jupyter: [https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html)

SounderSIPS Jupyter tutorials: [https://github.com/unity-sds/sounder-sips-tutorial](https://github.com/unity-sds/sounder-sips-tutorial)

**API endpoints**

WPS-T endpoint:

DAPA endpoint:

DAPA example: [https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py](https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py)

**System overview**

Jupyter is the primary interface to the system. For job-management activities you will interact with the WPS-T API provided by the SPS (Processing System) subsystem. For data input and output, you interact with the DAPA API from the DS (Data Store) subsystem. The ADS subsystem for Algorithm Development has several pieces, including code source-control and the Application Catalog (using Dockstore).

At the release of R2, the interface between Dockstore and the rest of the system is limited. It will eventually be possible to directly deploy applications from Dockstore into the scaled compute environment (SPS subsystem).

![](https://documents.lucid.app/documents/2eaf0390-bb79-4c4d-af02-e7f64e0914a3/pages/.2F-os\_15SZe?a=6270\&x=5292\&y=661\&w=1141\&h=1342\&store=1\&accept=image%2F\*\&auth=LCA%207b9b5677bf9594c38ff6dc4ac26c469452baa251-ts%3D1658948780)



