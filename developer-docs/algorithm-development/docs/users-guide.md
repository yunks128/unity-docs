# Users Guide

### Use case for algorithm development

For the following User Guide, we'll use the following use case for a simple algorithm that we want to run. We plan to download a data product from a DAAC, staging it for the processing, processing it, and then writing some outputs back into our data storage environment. The workflow for this might look like the following:

1. Identify data in CMR/Earthdata search that you want to process
2. Download the data to your environment
3. Process the data
4. Store the results (for further work, sharing, etc)

In the above example, the 'processing' can be as trivial or as complex as you'd like, we will do a relatively straight forward example here. Other examples can be found in our github and algorithm catalogs.&#x20;

For our processing example, we will simply dump the header of a netcdf file to an output file. \
\
`ncdump -h <myfile.nc> > myfile.hdr.txt`

### Getting Started

In general, the MDPS Algorithm Developer will focus on processing data _once it has been downloaded and is local to your code._ This is how it will run when packaged and sent to the processing systems within the MDPS. **However**, to test your code within the jupyter environment,  you will need to make data available on your own- that is, download and stage the data locally from a DAAC within the jupyter environment.

Once the data are local, you can begin to write an algorithm that will process the data based on that data location (**Hint:** That location of the data should be a variable within your code- it will not be known ahead of time). When you are satisfied with the code and the outputs (if any) being produced, it's time to add some code that will enable this to run in the MDPS environment.

After this "wrapper" code has been added, we will then use a set of tools or services to **build** the algorithm into an interoperable "application pacakge" that can be run on any underlying MDPS processing system.

### Inputs, outputs, and variables

An example application can be found here: [https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb](https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb) The following will use this as the basis of illustration.

Your code can expect a few things when executed, these include the location of data files you want to process, the location that output files should be written to, and any variables you want passed in- perhaps these are configuration parameters for your algorithm.

<figure><img src="../../../.gitbook/assets/Screenshot 2024-02-29 at 11.11.38 AM.png" alt=""><figcaption><p>Example Inputs, outputs, and variables <a href="https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb">https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb</a></p></figcaption></figure>

A final note about the cell above- it **must be tagged with the "parameters" tag** within the jupyter notebook. This will tell the algorithm build system that this is where input, output and variables are defined.

#### Inputs

Inputs is not a simple directory, it is a STAC catalog of data products staged in on the algorithm's behalf. The default value (`test/stage_in/stage_in_results.json`) is a default value that can be used when running in the jupyter environment. This value will be replaced by the location of the STAC catalog specifying staged in files at run time. MDPS provides a set of functions to read and parse this stac data.

You may notice a comment field `# type: stage-in` on the same line as the variable definition. This is key for the automated packing process to know that this algorithm expects some data to be staged-in.

**Note:** you do not need to have a stage-in parameter set, but you can have at most one variable definition annotated with `# type: stage-in`.

**Note:** If you want your code to run against test data (local to the jupyter environment) and data staged-in in the science processing systems, you will need to create STAC metadata for your test files. The tutorials notebook has examples of this.

#### Outputs

When your process runs, it will potentially be run in any number of nodes on any number of clusters. We can't write data to a disk and assume it will be discoverable by other algorithms or users. To persist this data to somewhere common- we must define the outputs of a process. Marking a variable as `# type: stage-out` will insert a directory to which you can write any output files to and will be available for long term storage. In order for these files to be persisted, however, a STAC `catalog.json` file with corresponding item files for each dataset must be written to that directory. Again, MDPS provides tools for creating this programmatically.

#### Variables

For any number of reasons, you might want to modify some value at run time. This is where the concept of variables comes in- any variable definition within a cell marked with the "parameters" tag, but not containing a stage-in or stage-out annotation will be treated as a variable. Variables should have a sensible default, and that default type will mark what kind of variable the field is (e.g. `"default"` will create a string default where as `0` will create an integer.

In our example application, say we wanted to dump a variable instead of just the header. Besides changing the command, we might want to be able to specify at runtime the variable to dump. So a field like \`\``netcdf_var_to_dump = "radiance"` that can be overridden at runtime might make sense. This could even be an array if we want to dump multiple variables.

**Note:** a good (required?) practice is to pass in an "output collection variable" to which all of the output files will belong. This output\_collection should be an already created collection within the unity environment. If it does not exist within the unity environment, the file will still be written to a persistent store, but will not be catalogged in the MDPS data catalog.

#### Input, outputs and variables - Key Takeaways

The key takeaway with regards to inputs and outputs are as follows:

1. To automatically stage input files, mark the variable with a `#type: stage-in` annotation. Stage-in types are not files or directories, but a **STAC Catalog** to be read.
2. To persist files after a run, the `#type: stage-out` annotation should be used, and will point to a directory to which all output files should be written.
3. Any output file you wanted persisted, but exist in a stac item file belonging to a catalog.json file.
4. MDPS provides tools to read and write these STAC files. See [https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb](https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb) for more details.

### Dependencies

Basic python is fine and all, but what if you have a lot of complex setup? Perhaps you require GDAL to be present on the system for some raster transformations, or your own custom python code to be installed? Or specific versions of platform software? The above examples is trivial in these regards, but take an example like the [SBG Preprocessing](https://github.com/unity-sds/SBG-unity-preprocess) example, with the following imports:

<figure><img src="../../../.gitbook/assets/Screenshot 2024-02-29 at 12.05.33 PM.png" alt=""><figcaption><p>An example of some complex imports not available in default installations</p></figcaption></figure>

When creating an algorithm with dependencies, we need to tell the system that these are required. When we are running interactively within the environment, we can simply do a `pip install` or a `conda install` to make the libraries available to our notebook.

When building a package for execution in the processing platform, we must specify these packages as build time. Because our packages are based on repo2docker, we are able to specify this in any of the underlying repo2docker supported mechanisms, but in practice we've found 2 methods that work very simply.

#### requirements.txt and runtime.txt

If your code is simply `pip install`ing dependencies, as long as your repository contains a requirements.txt file, those will be installed in the docker container that will execute your code. If you want to specify the python runtime of your process, include it as a single line in a 'runtime.txt' file:

> `cat runtime.txt`\
> `python-3.9`

**Note**: it's best practice to specify specific versions of libraries in your requirements.txt file so that the build is consistent.

#### environment.yml

For more complex software installs, including a conda environment.yml file will use that as the basis for the container built with your code. This can be combined with conda installs, pip installs, and even pip installs of github repositories where necessary.

**Note:** If both an environment.yml file and a requirements.txt file are specified, the environment.yml file is used. it will not install requirements.txt after installing the base environment!

