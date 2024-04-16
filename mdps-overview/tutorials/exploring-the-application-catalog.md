# Exploring the Application Catalog

After this tutorial, you'll be able to

* Log in and explore the application catalog
* Find an application with which to process data

The algorithm Catalog is used to store and make available access to algorithms and workflows for our processing system to use. It enables open science by making the executables and _how_ to run an algorithm available to all users. The underlying software for the application catalog is called [Dockstore](https://dockstore.org/).&#x20;

[Unity Dockstore Application Catalog](http://awslbdockstorestack-lb-1429770210.us-west-2.elb.amazonaws.com:9998/)

You do not need to log in to the dockstore environment to view files, but you will need to have a login to publish your own application packages.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-16 at 8.12.00 AM.png" alt=""><figcaption><p>Searching the Dockstore application catalog</p></figcaption></figure>

The main area for finding an application package is the "search" tab. You can peruse the application catalogs by paging through resutls, or searching on text, or even advanced search features.

**note:** The Unity application pacakges are all "workflow" based, and the usage of the "Tool" subtype is not currently supported.

Once you find an application package of interest, click on the link and you'll be brought to the application package details screen.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-16 at 8.15.28 AM.png" alt=""><figcaption><p>Application Package Overview</p></figcaption></figure>

The application package overview (or workflow overview as Dockstore calls it) gives some basic information on the application itself. The most interesting tabs available are the "Files" and "Versions" tabs.

### "Files" tab

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-16 at 8.17.07 AM.png" alt=""><figcaption><p>Example files for an application pacakge</p></figcaption></figure>

Application pacakges will (almost) always have several files available to them. Commonly you'll find a "Dockstore.cwl" file, a "workflow.cwl" file, a "process.cwl" file, and an optional "stage\_in.cwl" and "stage\_out.cwl" files. For the most part, you do not need to know what each of these files does or how its written, though there are some expert parameters that can be added if desired.

You might have noticed the "cwl" extenions in the files mentioned above. These are all examples of the [common workflow](https://www.commonwl.org/) specification.

Nine times out of ten, you'll only be interested in the "worfklow.cwl" file. This is where the inputs to a workflow are specified, and the "link" to this file is what you'll want to _deploy_ and execute in a processing system.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-16 at 8.23.54 AM.png" alt=""><figcaption><p>Example of copying the link to this application package for execution</p></figcaption></figure>

### "Versions" tab

<figure><img src="../../../.gitbook/assets/Screenshot 2024-04-16 at 8.16.03 AM.png" alt=""><figcaption><p>Previous Versions of an Application Catalog</p></figcaption></figure>

Algorithm development is almost always an iterative process. Each time you build, re-build, and publish you algorithm, either [manually](https://github.com/unity-sds/app-pack-generator) or through [Unity provided build services](the-development-environment/packaging-an-algorithm.md), a new version will be created. This allows you to compare previous versions to one another, as well as run an older version of the algorithm for provenance purposes.
