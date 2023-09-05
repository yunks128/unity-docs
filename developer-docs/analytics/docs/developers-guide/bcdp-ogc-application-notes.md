# BCDP OGC application notes

[https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb](https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb)After successfully pushing the docker image and generating the CWL files with the app-pack-generator, the resulting CWL files can be found in `/path/to/unity-analytics-bcdp/.unity_app_gen/cwl`

If you compare these generated files with the working set of CWL files found in the examples folder, you'll see some differences as the files were manually edited after being built in order to make the example work through U-SPS. Namely:

1. In workflow.cwl, remove all references to stage\_in/stage\_out. The BCDP app has no stage\_in/stage\_out steps. For the former, the input data is a publicly available dataset that is available directly from S3 via the fsspec/s3fs library and so does not need to be manually staged-in. As for stage\_out, this is currenty a work in progress but the `process.ipynb` notebook needs to be modified to write the output to a STAC catalog. See the Unity Example Application \[notebook]\([https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb](https://github.com/unity-sds/unity-example-application/blob/main/process.ipynb)) for an example.
2. If the docker image was not pushed through `build_ogc_app push_docker`, each CWL file must be modified to reference the correct registry location/tag of the build docker image.
3. inputs parameters in worfklow.cwl are contained inside a `parameters` section. Instead this should just be:

```
inputs:
  dataset: string
  backend: string
  dlat: float
  dlon: float
  method: string
```

4. After making the necessary modifications to the workflow.cwl and pushing them to the repo, the applicationDescriptor.json needs to link to the updated file:

```
  "processDescription": {
        "process": {
            "id": "unity-analytics-bcdp.58c0285a",
            "title": "Readd docker req to process.cwl\n",
            "owsContext": {
                "offering": {
                    "content": {
                        "href": "https://raw.githubusercontent.com/unity-sds/unity-analytics-bcdp/main/examples/cmip6-bucket/cwl/workflow.cwl"
                    }
                }
            },
```
