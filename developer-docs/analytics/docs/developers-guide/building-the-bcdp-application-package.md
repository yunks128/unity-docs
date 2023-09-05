---
description: Build BCDP OGC app using the Unity app-pack-generator
---

# Building the BCDP application package

Below is an overview for the process of building the BCDP application package. Examples of built BCDP workflows can also be found in the \[examples]\([https://github.com/unity-sds/unity-analytics-bcdp/tree/main/examples](https://github.com/unity-sds/unity-analytics-bcdp/tree/main/examples)) directory in the U-AS BCDP GitHub repository.

### Pre-requisites

* Docker (If running locally on MacOS, Docker Desktop is recommended and must be running.)
* Python 3.9+. Using conda/mamba, virtualenv, or poetry is recommended to create a clean environment.

### Installation

1. Install the Unity App Pack Generator. Setting up the installation in a clean environment with python 3.9+ (eg. via conda/mamba, virtualenv, or poetry):

```
pip install git+https://github.com/unity-sds/unity-app-generator.git 
```

2. Checkout the BCDP Application repo:

```
git clone https://github.com/unity-sds/unity-analytics-bcdp.git
cd /path/to/unity-analytics-bcdp
```

3. Initialize the app pack generator build process for the repo:

```
build_ogc_app init .
```

4. Build the docker image

```
build_ogc_app build_docker
```

5. Push the tagged docker image to a registry of your choice. The app-pack-generator has built-in support for dockstore via `build_ogc_app push_docker`, but here we have used DockerHub as an example:

```
docker push agoodm/unity-analytics-bcdp:8f73bc0d
```

6. Generate the CWL files. The output should show the notebook parameters being parsed:

```
build_ogc_app build_cwl
```

```
DEBUG:unity_app_generator.state:Reading existing application state directory /Users/goodman/dev/unity-analytics-bcdp/.unity_app_gen
DEBUG:app_pack_generator.git:Using existing Git repository from destination path /Users/goodman/dev/unity-analytics-bcdp with source /Users/goodman/dev/unity-analytics-bcdp
DEBUG:docker.utils.config:Trying paths: ['/Users/goodman/.docker/config.json', '/Users/goodman/.dockercfg']
DEBUG:docker.utils.config:Found file at path: /Users/goodman/.docker/config.json
DEBUG:docker.auth:Found 'auths' section
DEBUG:docker.auth:Auth data for https://index.docker.io/v1/ is absent. Client might be using a credentials store instead.
DEBUG:docker.auth:Found 'credsStore' section
DEBUG:urllib3.connectionpool:http://localhost:None "GET /version HTTP/1.1" 200 None
DEBUG:app_pack_generator.application:Reading notebook: "/Users/goodman/dev/unity-analytics-bcdp/process.ipynb"
DEBUG:app_pack_generator.application:Validating /Users/goodman/dev/unity-analytics-bcdp/process.ipynb as a valid v4.0 - v4.5 Jupyter notebook
DEBUG:app_pack_generator.application:Successfully validated using "nbformat.v4.0.schema.json"
INFO:papermill:Input Notebook:  /Users/goodman/dev/unity-analytics-bcdp/process.ipynb
INFO:unity_app_generator.generator:Parameters:
name     inferred_type    cwl_type    default                                                                                help
-------  ---------------  ----------  -------------------------------------------------------------------------------------  ------
dataset  str              string      s3://cmip6-pds/CMIP6/CMIP/NCAR/CESM2-WACCM/historical/r1i1p1f1/Amon/tas/gn/v20190227/
backend  str              string      scipy
method   str              string      linear
dlon     int              int         5
dlat     int              int         5
nc_file  stage-out        File        CMIP_example.nc

```

