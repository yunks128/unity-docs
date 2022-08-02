---
description: Unity Prototype Release 2 Links to Get Started
---

# Starting Line

**Getting started**

1. First you will need to confirm access to the Unity test environment. You will need to make a MCP access request to the support team if it does not already work. The core user interface will be [JupyterLab](https://jupyter-test-alb-1374844384.us-west-2.elb.amazonaws.com:8000/).
2. Once you have JupyterLab access set up, we suggest trying out the official JupyterLab tutorial at [https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html). This will help you get familiar with the Jupyter user interface and concepts.
3. Next, try the "Working with data" Python notebook tutorial in the /tutorials folder. This will get you started learning some of the Unity APIs and how to find data from Jupyter.



**System overview**

Unity is a set of cloud services that provide an end-to-end tool set for science analysis, algorithm development, and scaled processing. They key service areas available are:

ADS: the Algorithm Development service, which includes a JupyterLab environment, code source-control and the Application Catalog (implemented using Dockstore).

SPS: For job-management activities you will interact with the WPS-T API provided by the SPS (Processing System) subsystem.&#x20;

DS: For data input and output, you interact with the DAPA API from the DS (Data Store) subsystem.

Jupyter is the primary graphical interface to the system.&#x20;

At the release of R2, the interface between Dockstore and the rest of the system is limited. It will eventually be possible to directly deploy applications from Dockstore into the scaled compute environment (SPS subsystem). The user interface to do so will evolve with system capabilities.

**An overview of the Unity Prototype Release 0.2 system**

Blue boxes indicate graphical user-interfaces. Orange boxes are other service subsystems, with grey dotted-border boxes indicating APIs used to interact with those subsystems.

Initially the two GUIs are&#x20;

1\) Jupyter, where the system may be interfaced via the indicated APIs, and

2\) Dockstore, where the Application Packages are stored

![](https://documents.lucid.app/documents/2eaf0390-bb79-4c4d-af02-e7f64e0914a3/pages/.2F-os\_15SZe?a=6273\&x=5292\&y=661\&w=1141\&h=1342\&store=1\&accept=image%2F\*\&auth=LCA%204ffb33e23d9c8212fca25bf036f885be2e9a068b-ts%3D1659395518)

**Links and references**

JupyterLab home: (dev) [https://jupyter-test-alb-1374844384.us-west-2.elb.amazonaws.com:8000/](https://jupyter-test-alb-1374844384.us-west-2.elb.amazonaws.com:8000/)

Working with Jobs tutorial: [https://github.com/unity-sds/sounder-sips-tutorial/blob/develop/jupyter-notebooks/tutorials/3\_working\_with\_jobs.ipynb](https://github.com/unity-sds/sounder-sips-tutorial/blob/develop/jupyter-notebooks/tutorials/3\_working\_with\_jobs.ipynb)

Dockstore workflow list (Application Catalog): [http://ec2-52-25-78-158.us-west-2.compute.amazonaws.com:9998/workflows](http://ec2-52-25-78-158.us-west-2.compute.amazonaws.com:9998/workflows)

Dockstore user guide: [https://docs.dockstore.org/en/stable/getting-started/getting-started.html](https://docs.dockstore.org/en/stable/getting-started/getting-started.html)

Learning Jupyter: [https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html)

SounderSIPS Jupyter tutorials: [https://github.com/unity-sds/sounder-sips-tutorial](https://github.com/unity-sds/sounder-sips-tutorial)

**API endpoints and references**

WPS-T endpoint: [http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/processes](http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/processes) and others at the same location; see WPS-T API reference below

WPS-T API reference: [http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/api/docs](http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/api/docs)

Job/process status dictionary: [https://hysds-core.atlassian.net/wiki/spaces/HYS/pages/527761452/Job+Workflow+in+HySDS](https://hysds-core.atlassian.net/wiki/spaces/HYS/pages/527761452/Job+Workflow+in+HySDS)

DAPA endpoints: [https://58nbcawrvb.execute-api.us-west-2.amazonaws.com/test/am-uds-dapa/collections](https://58nbcawrvb.execute-api.us-west-2.amazonaws.com/test/am-uds-dapa/collections) and others are the same location; see DAPA API reference below

DAPA API reference: [https://docs.ogc.org/per/20-025r1.html#\_dapa\_overview](https://docs.ogc.org/per/20-025r1.html#\_dapa\_overview)

DAPA example: [https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py](https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py)

