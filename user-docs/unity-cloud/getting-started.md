---
description: One small click for a mouse, one giant leap for science data system users
---

# Getting Started

This page will cover the following topics

* Getting Access to the Unity-SDS system
* Logging in for the first time

### Getting Access

1. The Unity (test) environment runs on NASA's Mission Cloud Platform (MCP) - which you will need access to first in order to access Unity. E-mail \`AGCY-MissionCloud@mail.nasa.gov\` for questions.&#x20;
   1. NOTE: the core Unity user interface leverages [Jupyter](https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/). To understand the first-time log in experience please see the [Common Services documentation](../../developer-docs/common-services/).
2. After you've confirmed access to the MCP platform, you'll need to ensure you can access the Unity environment. Please [contact the Unity team](../../getting-help/contact-us.md) for further directions.
3. Confirm you can access the Unity JupyterLab interface (test) environment: [https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/](https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/)
   1. We suggest trying out the official Jupyter welcome tour ([https://jupyter.org/try-jupyter/lab/](https://jupyter.org/try-jupyter/lab/)) and user documentation ([https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html)). This will help you get familiar with the Jupyter user interface and concepts.



### Logging In

1. Open the following URL in a web browser: [https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/](https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/)
2. The next step is to clone the [sounder-sips-tutorial code repository](https://github.com/unity-sds/sounder-sips-tutorial) into your jupyter notebook, and follow along with the tutorials. The README in the code repo (link above) has instructions on how to clone it into the Jupyter environment.

### Next Steps

TBD: See [Tutorials](tutorials/) for key next steps to follow.

### Useful Links

#### References

* JupyterLab home: (test) [https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/](https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/)
* Job/process status dictionary: [https://hysds-core.atlassian.net/wiki/spaces/HYS/pages/527761452/Job+Workflow+in+HySDS](https://hysds-core.atlassian.net/wiki/spaces/HYS/pages/527761452/Job+Workflow+in+HySDS)
* Working with Jobs tutorial: [https://github.com/unity-sds/sounder-sips-tutorial/blob/develop/jupyter-notebooks/tutorials/3\_working\_with\_jobs.ipynb](https://github.com/unity-sds/sounder-sips-tutorial/blob/develop/jupyter-notebooks/tutorials/3\_working\_with\_jobs.ipynb)
* Dockstore workflow list (Application Catalog): [http://uads-test-dockstore-deploy-lb-1762603872.us-west-2.elb.amazonaws.com:9998/workflows](http://uads-test-dockstore-deploy-lb-1762603872.us-west-2.elb.amazonaws.com:9998/workflows)
* DAPA API reference: [https://docs.ogc.org/per/20-025r1.html#\_dapa\_overview](https://docs.ogc.org/per/20-025r1.html#\_dapa\_overview)
* DAPA example: [https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py](https://github.com/unity-sds/unity-cli/blob/main/unity/commands/ds.py)
* Dockstore user guide: [https://docs.dockstore.org/en/stable/getting-started/getting-started.html](https://docs.dockstore.org/en/stable/getting-started/getting-started.html)
* Learning Jupyter: [https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html](https://jupyterlab.readthedocs.io/en/stable/getting\_started/overview.html)
* SounderSIPS Jupyter tutorials: [https://github.com/unity-sds/sounder-sips-tutorial](https://github.com/unity-sds/sounder-sips-tutorial)

#### Application Programming Interfaces (APIs)

* WPS-T endpoint: [http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/processes](http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/processes) and others at the same location; see WPS-T API reference below
* WPS-T API reference: [http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/api/docs](http://af9ab68f04f5a470bb55ba29ff6a6215-1326373853.us-west-2.elb.amazonaws.com:5001/api/docs)
* DAPA endpoints: [https://58nbcawrvb.execute-api.us-west-2.amazonaws.com/test/am-uds-dapa/collections](https://58nbcawrvb.execute-api.us-west-2.amazonaws.com/test/am-uds-dapa/collections) and others are the same location

See [Endpoints](endpoints.md) for a more comprehensive list of APIs available. &#x20;

