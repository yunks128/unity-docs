# Advanced Algorithm Development Topics

## Custom Libraries

More often than note, you'll require custom libraries to do something of value in an algorithm. Perhaps its netcdf, numpy, scipy, rasterio, or some other required library or custom code. While the code for executing your algorithm is most likely in a jupyter notebook, the libraries it depends on should be specified in separate file. Because the [application package build](packaging-an-algorithm.md) system uses repo2docker, this is well documented at [https://repo2docker.readthedocs.io/en/latest/config\_files.html](https://repo2docker.readthedocs.io/en/latest/config\_files.html). In short one can provide:

* a [conda environment file](https://github.com/unity-sds/SBG-unity-preprocess/blob/unity-sbg-preprocess-1.0/environment.yml)
* a [requirements.txt](https://github.com/unity-sds/SBG-unity-preprocess/blob/unity-sbg-preprocess-1.0/requirements.txt) file
* Other repo2docker supported files (Julia, R, and others, though these have not been exhustively tested by the Unity team

{% hint style="warning" %}
Note: there is an order of precedence in the configuration files. For example, if an environment.yml (conda) file is present, it will be used over any other file (e.g. ignoring a requirements.txt file)
{% endhint %}

## Version Control (Github)

At this time, the Unity system requires that a notebook live in a repository to provide automatic package generation. Furthermore, the repository must be public. Manual builds are available for software that cannot be made public. Please work with the Unity team to set this up for your software. Further more, only a single algorithm can be housed in a repository. So if you have five algorithms you want to work on, each will have its own repository.

