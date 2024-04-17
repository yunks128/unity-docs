# Packaging an Algorithm

This page describes how to convert and publish your notebook into an executable application package.

## Using the Automated Build System

Follow the [jupyter notebook tutorial for automatically building an application package](https://github.com/unity-sds/sounder-sips-tutorial/blob/main/jupyter-notebooks/tutorials/5\_building\_application\_packages.ipynb).

{% hint style="warning" %}
Currently, the app pack gen service does not provide much information on status or result of the builds. This is an on-going process on which improvements are being made.&#x20;
{% endhint %}

The built application packages can currently be found under the `edwinsarkissian` user name in dockstore- another change we will be making in the near future.

## Manually Building the Application Package

This will allow you to build your own application packages from Jupyter notebooks. Note, a container (e.g. docker) build engine is required to do this!

### Install the Unity App Generator

A requirement for using the unity application generator is to install it. Currently the install must be manual.

```
$ pip install git+https://github.com/unity-sds/app-pack-generator.git
...
$ pip install git+https://github.com/unity-sds/unity-app-generator.git 
...
$ build_ogc_app -h
usage: build_ogc_app [-h] [--state_directory STATE_DIRECTORY] {init,build_docker,push_docker,parameters,build_cwl,push_app_registry} ...

Unity Application Package Generator

positional arguments:
  {init,build_docker,push_docker,parameters,build_cwl,push_app_registry}
    init                Initialize a Git repository for use by this application. Creates a .unity_app_gen directory in the destination directory
    build_docker        Build a Docker image from the initialized application directory
    push_docker         Push a Docker image from the initialized application directory to a remote registry
    parameters          Display parsed notebook parameters
    build_cwl           Create OGC compliant CWL files from the repository and Docker image
    push_app_registry   Push CWL files to Dockstore application registry

options:
  -h, --help            show this help message and exit
  --state_directory STATE_DIRECTORY
                        An alternative location to store the application state other than .unity_app_gen
```

### Build an Application

We are now ready to build an application! We will build the [unity-example-application](https://github.com/unity-sds/unity-example-application).

#### Initialize the repository for building

Initialize the respository we want to build. If you want to checkout the repository from git to build, run the following command:

```
$ build_ogc_app init https://github.com/unity-sds/unity-example-application my-example-app
DEBUG:app_pack_generator.git:Cloning Git repository from https://github.com/unity-sds/unity-example-application to my-example-app
DEBUG:git.cmd:Popen(['git', 'clone', '-v', '--', 'https://github.com/unity-sds/unity-example-application', 'my-example-app'], cwd=/home/gangl/tutorial, stdin=None, shell=False, universal_newlines=True)
DEBUG:git.repo.base:Cmd(['git', 'clone', '-v', '--', 'https://github.com/unity-sds/unity-example-application', 'my-example-app'])'s unused stdout: 
DEBUG:unity_app_generator.state:Initializing new application state directory /home/gangl/tutorial/my-example-app/.unity_app_gen
DEBUG:docker.utils.config:Trying paths: ['/home/gangl/.docker/config.json', '/home/gangl/.dockercfg']
DEBUG:docker.utils.config:Found file at path: /home/gangl/.docker/config.json
DEBUG:docker.auth:Found 'auths' section
DEBUG:docker.auth:Found entry (registry='https://index.docker.io/v1/', username='gangl')
DEBUG:urllib3.connectionpool:http://localhost:None "GET /version HTTP/1.1" 200 846

```

If you have an existing repository you'd like to build, you can run the init command from inside that repo:

```
$ cd unity-application-package #my existing git repo working directory
$ build_ogc_app init .
```

#### Build and push the docker container

We are now ready to build the docker file for our application. This can take a while initially as it needs to pull images, install dependencies, and your notebook for builds. Once built. updates to the jupyter notebook are built relatively quickly as it's the last step in the build process.

From within the repository you'd like to build (e.g. `cd unity-example-application` or `cd my-example-app`

```
$  build_ogc_app build_docker --no_owner
build_ogc_app build_docker --no_owner
DEBUG:unity_app_generator.state:Reading existing application state directory /home/gangl/tutorial/my-example-app/.unity_app_gen
DEBUG:app_pack_generator.git:Using existing Git repository from destination path /home/gangl/tutorial/my-example-app with source https://github.com/unity-sds/unity-example-application
DEBUG:docker.utils.config:Trying paths: ['/home/gangl/.docker/config.json', '/home/gangl/.dockercfg']
DEBUG:docker.utils.config:Found file at path: /home/gangl/.docker/config.json
DEBUG:docker.auth:Found 'auths' section
DEBUG:docker.auth:Found entry (registry='https://index.docker.io/v1/', username='gangl')
DEBUG:urllib3.connectionpool:http://localhost:None "GET /version HTTP/1.1" 200 846
DEBUG:git.cmd:Popen(['git', 'cat-file', '--batch-check'], cwd=/home/gangl/tutorial/my-example-app, stdin=<valid stream>, shell=False, universal_newlines=False)
DEBUG:app_pack_generator.docker:Building Docker image named unity-example-application:9ecce051
....
Successfully tagged unity-example-application:9ecce051
$
```

We use the "no-owner" command above so that we do not include the unity\_sds organization in the built container. This makes it easier to push the container to a registry.

```
$ docker login
...
$ build_ogc_app push_docker <docker_hub_username>
...
```

You can push this to any repository you like, but the default is to use the open platform dockerhub. You'll need a username and account on dockerhub to push a container to. Before running the above command, make sure you login!

#### Build and publish the CWL

the next step is to build the CWL for the application package. This is done by looking at the notebook for any stage-in or stage-out annotations, as well as the 'parameters' cell. A summary of the parameters found and their inferred\_type will be displayed to you after completion:

```
$ build_ogc_app build_cwl
...
name                        inferred_type    cwl_type    default                              help
--------------------------  ---------------  ----------  -----------------------------------  ------------------------------
input_stac_collection_file  stage-in         string      test/stage_in/stage_in_results.json
output_stac_catalog_dir     stage-out        File        test/process_results/
summary_table_filename      str              string      summary_table.txt
output_collection           str              string      example-app-collection___1
example_argument_int        int              int         1
example_argument_float      float            float       1.0
example_argument_string     str              string      string
example_argument_bool       bool             boolean     True
example_argument_empty      string           string                                           Allow a null value or a string
DEBUG:git.cmd:Popen(['git', 'cat-file', '--batch-check'], cwd=/home/gangl/tutorial/my-example-app, stdin=<valid stream>, shell=False, universal_newlines=False)
DEBUG:git.cmd:Popen(['git', 'cat-file', '--batch'], cwd=/home/gangl/tutorial/my-example-app, stdin=<valid stream>, shell=False, universal_newlines=False)
```

Finally, we want to publish this to the Unity application catalog to reference by a processing system. For this you'll need a dockstore token and the url of the dockstore API.

```
$ build_ogc_app push_app_registry --api_url http://awslbdockstorestack-lb-1429770210.us-west-2.elb.amazonaws.com:9998/api --token ***
...
```

That's it! your application is now registered in the Unity application catalog.&#x20;

### Generating a dockstore token

You can only get a token from within the dockstore UI after logging in.

To view your token, go to your account:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-17 at 9.58.10 AM.png" alt=""><figcaption><p>Access your account information</p></figcaption></figure>

And then copy (or view) your dockstore token:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-17 at 9.59.07 AM.png" alt=""><figcaption><p>Finding your token for REST access</p></figcaption></figure>

