# Understanding data access in the Unity System

## File Availability and Data Access&#x20;

Many algorithm developers are used to writing code that uses input data files and parameters and outputs other files.  Often times the division between the algorithm and the file storage system is blurred- assumptions are made about where data are and how they are stored for improved access. For example, we look at /data/inputs for input data, or can glob on the filename to find the date and time of files we need.

There are two opinionated steps the Unity system has made that need to be understood for algorithm development.

**The Unity system separates  the **_**discovery**_** of data from the execution of the algorithm**.&#x20;

> This makes the code more portable- it's no longer dependent on a specific file system or directory structure. This means the algorithm can be shared with other users that might not have access to a specific file system (e.g. it's only accessible on a NASA VPN).  Similarly, it can be run 'anywhere' - that is in a different _environment_ (the cloud, a laptop, a super compute center) or in a different _venue_ (e.g. a development, test, production, or reprocessing environment).&#x20;

&#x20;**The Unity system separates the **_**staging**_** of data from the execution of the algorithm.** &#x20;

> No assumptions about _how_ the data are made available to the algorithm (or how the algorithm gets to the data) are made. Many algorithms contain _implementations_ such as copy (`cp`), download (`wget, curl`), s3 (`aws s3 cp...`), or others. There are many assumptions made when these types of commands are used from file structures, availability of the servers, usernames/passwords and credentials.

How data are made available to the algorithm is dependent on the **platform**. For example, if you are running the algorithm on your laptop, the laptop owner is responsible for downloading or copying the required data to their laptop. In an ephemeral cloud instance (such as `ec2`), the data must be made available at the time of processing.

With these 2 assumptions in mind, the algorithm ( and algorithm developer) should be written as if the exact files needed will be placed in a readable directory at run time. So we must answer two questions:

1. How does the algorithm determine where the data, magically made available to it, are stored so they can be read and processed? This is answered in the [Algorithm Data Access](users-guide.md#algorithm-data-access) section below.
2. When we execute an algorithm, how do we define what data we want processed? This is answered in [Identifying data for processing](users-guide.md#identifying-data-for-processing).

## Algorithm data access

For the following discussion we'll look at a "real" algorithm developed in the unity system. The notebook can be found here: [https://github.com/unity-sds/SBG-unity-preprocess/blob/unity-sbg-preprocess-1.0/process.ipynb](https://github.com/unity-sds/SBG-unity-preprocess/blob/unity-sbg-preprocess-1.0/process.ipynb)&#x20;

When we develop an algorithm in the Unity system, we assume the data are "next to" the algorithm. To tell the system we're expecting data, we define a `stage-in` item as our inputs:

```python
# The defaults used here generally relflect a local or jupyter environment; they are replaced with "runtime" values when run in the system.
input_stac_collection_file = '/unity/ads/inputs/L1B/catalog.json' # type: stage-in
```

{% hint style="danger" %}
Note, when defining the above code block, it is vital to 'tag' it within the jupyter environment as `parameters` this is how the system introspection knows we're telling it to make data available for the algorithm.
{% endhint %}

You might be asking yourself, what the heck is `catalog.json` and why is it at `/unity/ads/inputs/L1B` directory- Didn't I just say we're separating discovery and directory structures from the algorithm? And what do i _do_ with that file?

### What the heck is catalog.json?

Catalog.json is a [STAC](https://stacspec.org/) file that contains pointers to _**assets**_. For the most part, asset is synonymous with **datafile.** it can contain a single file or thousands, depending on what was staged. This file is how an algorithm knows what data were made available, and where those files are on the disk local to the algorithm.

### Why is catalog.json at that specific location?

In the above example we point to `/unity/ads/inputs/L1B/catalog.json` which seems awfully specific. When we run this notebook within a jupyterhub environment, the notebook will actually look for a STAC catalog at the /unity/ads/inputs/L1B location- this is done so that we can test the notebook locally. As an algorithm developer, you will get to choose a location available within your jupyter environment to store test data files, you'll also be responsible for generating STAC files for those staged products- but we have tooling to assist in that. If you were developing this algorithm on a laptop, you would most likely have some sample data available to quickly iterate on- this is the same thing.

As for that specific directory structure and our promise of portability and separate staging, by labeling this line of code with the `# type: stage-in` annotation, we are telling our system to turn this option into a **run-time parameter**. So while we see \``/unity/ads/inputs/L1B/catalog.json`when running the notebook within jupyter, it will be replaced with the actual location of the catalog when executed in the scalable, Unity processing **platform**. Again, the platform decides how and where to stage data for the algorithm, including login, retries, throughput, and more.

### Ok, how does my algorithm use that catalog?

The more you know about STAC, the more you'll be able to leverage it and its capabilities. But even if you're only aware that STAC is a format for identifying data products and their locations, you'll be able to use [Unity provided tooling](https://pypi.org/project/unity-sds-client/) to make find process these files. Consider the following code block that will read the STAC catalog and allow you to find the files to process:

```python
# required imports
from unity_sds_client.resources.collection import Collection
from unity_sds_client.resources.dataset import Dataset
from unity_sds_client.resources.data_file import DataFile

input_collection = Collection.from_stac(input_stac_collection_file)
data_filenames = input_collection.data_locations()

# data_filenames:
# ['/unity/ads/inputs/L1B/./EMIT_L1B_RAD_001_20231206T160939_2334011_006.nc',
# '/unity/ads/inputs/L1B/./EMIT_L1B_OBS_001_20231206T160939_2334011_006.nc']

for f in data_filenames:
    if "RAD" in f:
        radiance_file = f
    elif "OBS" in f:
        observation_file = f

print("OBS:" + observation_file)
# OBS:/unity/ads/inputs/L1B/./EMIT_L1B_OBS_001_20231206T160939_2334011_006.nc

print("RAD:" + radiance_file)
# RAD:/unity/ads/inputs/L1B/./EMIT_L1B_RAD_001_20231206T160939_2334011_006.nc
```

We can now simply open the radiance\_file or observation\_file and process them as per the algorithm instructions. Anything you decide to do after this is straight python (or whatever language you choose to use).

## Identifying Data for Processing

{% hint style="info" %}
A prerequisite for processing data in the Unity science processing system (SPS) would be to [convert your algorithm to an application package](packaging-an-algorithm.md).&#x20;
{% endhint %}

So how do we tell an algorithm what data it is meant to process? In the above SBG-preprocess example we worked with some local observation and radiance files as test inputs, but when running in the processing platform, we'll want to run the algorithm many times, and with many different data inputs. On our laptop, we might do something like the following:

```
for each input_file in directory:
  run my_algorithm.py --input input_file --output output_directory
  
```

This would execute the algorithm for each file, writing an output file somewhere else. In the Unity system we do the same type of thing, but instead of pointing to individual files or directories to process, we provide **STAC catalogs** of the data we need to process. STAC catalogs can be manually created, but many data providers provide STAC as a response type for their search engines. Notably the Earthdata Common Metadata Repository (e.g. CMR) and the Unity Data Catalog provide STAC as a response format. This makes it trivial to provide STAC data to your algorithms.&#x20;

For example, the following CMR query is a valid input when running the process:

{% embed url="https://cmr.earthdata.nasa.gov/search/granules.stac?collection_concept_id=C2408009906-LPCLOUD&temporal[]=2023-08-10T03:41:03.000Z,2023-08-10T03:41:03.000Z" %}

This results in a STAC entry pointing to the following assets:

```json
"assets" : {
      "metadata" : {
        "href" : "https://cmr.earthdata.nasa.gov:443/search/concepts/G2752969350-LPCLOUD.xml",
        "type" : "application/xml"
      },
      "browse" : {
        "title" : "Download EMIT_L1B_RAD_001_20230810T034101_2322203_002.png",
        "href" : "https://data.lpdaac.earthdatacloud.nasa.gov/lp-prod-public/EMITL1BRAD.001/EMIT_L1B_RAD_001_20230810T034101_2322203_002/EMIT_L1B_RAD_001_20230810T034101_2322203_002.png",
        "type" : "image/png"
      },
      "opendap" : {
        "title" : "OPeNDAP request URL",
        "href" : "https://opendap.earthdata.nasa.gov/collections/C2408009906-LPCLOUD/granules/EMIT_L1B_RAD_001_20230810T034101_2322203_002"
      },
      "data" : {
        "title" : "Download EMIT_L1B_RAD_001_20230810T034101_2322203_002.nc",
        "href" : "https://data.lpdaac.earthdatacloud.nasa.gov/lp-prod-protected/EMITL1BRAD.001/EMIT_L1B_RAD_001_20230810T034101_2322203_002/EMIT_L1B_RAD_001_20230810T034101_2322203_002.nc"
      },
      "data1" : {
        "title" : "Download EMIT_L1B_OBS_001_20230810T034101_2322203_002.nc",
        "href" : "https://data.lpdaac.earthdatacloud.nasa.gov/lp-prod-protected/EMITL1BRAD.001/EMIT_L1B_RAD_001_20230810T034101_2322203_002/EMIT_L1B_OBS_001_20230810T034101_2322203_002.nc"
      }
    }
```

The **platform** will _localize (e.g. Download)_ the data files and make them available to your algorithm. The algorithm developer does not need to worry about the localization step, just know the above files will be staged and accessible.

So to run our algorithm against multiple inputs, we'd simply execute the process with various _input_ STAC catalogs. in the below _contrived_ example, there is a separate product per minute:

```
https://cmr.earthdata.nasa.gov/search/granules.stac?collection_concept_id=C2408009906-LPCLOUD&temporal[]=2023-08-10T03:41:03.000Z,2023-08-10T03:41:03.000Z
https://cmr.earthdata.nasa.gov/search/granules.stac?collection_concept_id=C2408009906-LPCLOUD&temporal[]=2023-08-10T03:42:03.000Z,2023-08-10T03:42:03.000Z
https://cmr.earthdata.nasa.gov/search/granules.stac?collection_concept_id=C2408009906-LPCLOUD&temporal[]=2023-08-10T03:43:03.000Z,2023-08-10T03:43:03.000Z
```

