# Users Guide

### Use case for algorithm development

For the following User Guide, we'll use the following use case for a simple algorithm that we want to run. We plan to download a data product from a DAAC, staging it for the processing, processing it, and then writing some outputs back into our data storage environment. The workflow for this might look like the following:

1. Identify data in CMR/Earthdata search that you want to process
2. Download the data to your environment
3. Process the data
4. Store the results (for further work, sharing, etc)

In the above example, the 'processing' can be as trivial or as complex as you'd like, we will do a relatively straight forward example here. Other examples can be found in our github and algorithm catalogs.&#x20;

### Getting Started

Im general, the MDPS Algorithm Developer will focus on processing data _once it has been downloaded and is local to your code._ This is how it will run when packaged and sent to the processing systems within the MDPS. **However**, to test your code within the jupyter environment,  you will need to make data available on your own- that is, download and stage the data locally from a DAAC within the jupyter environment.

Once the data are local, you can begin to write an algorithm that will process the data based on that data location (**Hint:** That location of the data should be a variable within your code- it will not be known ahead of time). When you are satisfied with the code and the outputs (if any) being produced, it's time to add some code that will enable this to run in the MDPS environment.

After this "wrapper" code has been added, we will then use a set of tools or services to **build** the algorithm into an interoperable "application pacakge" that can be run on any underlying MDPS processing system.

### Inputs, outputs, and variables

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





