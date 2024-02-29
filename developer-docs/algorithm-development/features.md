# Features

### Development Environment (IDE)

The Development environment contains **a venue specific** set of resources available to the algorithm developer. CPU and Memory availabe to a user is dependent on the underlying resources specified during the creation time, but is adjustable by projects or MMO operators.&#x20;

Each environment contains a jupyter and conda environment for dependency management. Local disk space is available (defaults to 10GB) but an 'unlimited' storage area is also mounted to the instance for larger, persistent files.&#x20;

The jupyter instance has permissions to read from your venues data buckets as well, so utilizing AWS functions to read data products or list the contents of a bucket are available to you.

Lastly, the jupyter environment is meant to allow for programmatic access of Unity data and services, but also allow for the development and deployment of algorithms into the MDPS system. The development and deployment of algorithms is done through a algorithm build process, which requires a few annotations and structures in place to work effectively- all of this can be done from the development Environment.

### Algorithm Catalog

The Algorithm Development services also include a catalog of available algorithms to use or publish to. To deploy an algorithm within the unity system, it _should_ be registered within the Unity Algorithm Catalog, thought it is not strictly necessary. Publishing to a common catalog allows the versioning of algorithms as they are updated, and provide a way to share algorithms with other MDPS users- either within the project or externally, promoting open science.

### Algorithm Builds

To run an algorithm catalog in the MDPS, it must adhere to a few standardized concepts- it must be containerized with all of its dependencies, and it must be wrapped in the [common workflow language](http://www.commonwl.org/) (CWL). The MDPS provides services and tools to build these compliant algorithms using an online service (based on a github repository) or a set of tools that allows you to build the package yourself. Once built, the algorithm can be registered and used within the MDPS system.
