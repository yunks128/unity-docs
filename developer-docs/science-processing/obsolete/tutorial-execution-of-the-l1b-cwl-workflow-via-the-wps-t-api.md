# Tutorial: Execution of the L1B CWL Workflow via the WPS-T API



#### Step 1 (optional) - Deploy a U-SPS Cluster in the MCP Dev Account

* **Note:** You can skip this step if you're using the WPS-T API of a cluster that has already been deployed. You'll just need the URL of the API.

```shell
(local-machine) $ cd unity-sps-prototype/terraform-unity
(local-machine) $ terraform apply -var-file=MCP_DEV.tfvars -no-color 2>&1 --auto-approve | tee apply_output.txt
# Get the hostnames of the load balancers
(local-machine) $ terraform output
load_balancer_hostnames = {
  "ades_wpst_api" = "a61fe24ebf181450c91f845f49630a2d-573246423.us-west-2.elb.amazonaws.com"
  "grq_es" = "a4ac1feb65ed446ffae2a60f8a01f21e-1203473866.us-west-2.elb.amazonaws.com"
  "grq_rest_api" = "adf7c0237965749158216c7e43fb9203-1683284578.us-west-2.elb.amazonaws.com"
  "hysds_ui" = "a67db14bb36e84cc3bffb9a2b061d388-144872164.us-west-2.elb.amazonaws.com"
  "minio" = "ad346401d217d468cbda175157942365-784443646.us-west-2.elb.amazonaws.com"
  "mozart_es" = "a66702fe312dd422c9f954124e24f26c-356732983.us-west-2.elb.amazonaws.com"
  "mozart_rest_api" = "a22fbab5f394240a5b4eed0571b84b40-1028912979.us-west-2.elb.amazonaws.com"
}
# Set the the value of WPST_API for future API requests
(local-machine) WPST_API=a61fe24ebf181450c91f845f49630a2d-573246423.us-west-2.elb.amazonaws.com
```

#### Step 2 - Deploy a Process Named `l1b-cwl` with the WPS-T API

* First check if any processes are already registered:

```shell
curl -s "http://${WPST_API}:5001/processes" |  jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "processes": []
}
```

</details>

* Deploy the process.
* This request will do the following:
  * Build a Docker container image for the `l1b-cwl` PGE and upload it to the Github Container Registry (GHCR). The image will also be registered in the Mozart Elasticsearch.
  * Build a HySDS `hysds-io` document and register it in the GRQ Elasticsearch.
  * Build a HySDS `job-spec` document and register it in the Mozart Elasticsearch.
* **Note:** The CWL file to be run is specified as a raw GitHub URL in the request body.

```shell
curl -s -0 -X POST "http://${WPST_API}:5001/processes" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF | jq
{
   "processDescription":{
      "process":{
         "id":"l1b-cwl",
         "title":"l1b_pge_cwl",
         "owsContext":{
            "offering":{
               "code":"http://www.opengis.net/eoc/applicationContext/cwl",
               "content":{
                  "href":"https://raw.githubusercontent.com/unity-sds/unity-sps-workflows/main/sounder_sips/ssips_L1b_workflow.cwl"
               }
            }
         },
         "abstract":"l1b_pge_cwl",
         "keywords":[
         ],
         "inputs":[
         	{
               "id":"input_collection_id",
               "title":"input_collection_id",
               "formats":[
                  {
                     "mimeType":"text",
                     "default":true
                  }
               ]
            },
            {
               "id":"start_datetime",
               "title":"start_datetime",
               "formats":[
                  {
                     "mimeType":"text",
                     "default":true
                  }
               ]
            },
            {
               "id":"stop_datetime",
               "title":"stop_datetime",
               "formats":[
                  {
                     "mimeType":"text",
                     "default":true
                  }
               ]
            },
            {
            	"id": "output_collection_id",
            	"title": "output_collection_id",
            	"formats":[
                  {
                     "mimeType":"text",
                     "default":true
                  }
               ]
            }
         ],
         "outputs":[
            {
               "id":"output",
               "title":"L1B-product",
               "formats":[
                  {
                     "mimeType":"image/tiff",
                     "default":true
                  }
               ]
            }
         ]
      },
      "processVersion":"develop",
      "jobControlOptions":[
         "async-execute"
      ],
      "outputTransmission":[
         "reference"
      ]
   },
   "immediateDeployment":true,
   "executionUnit":[
      {
         "href":"docker.registry/ndvims:latest"
      }
   ],
   "deploymentProfileName":"http://www.opengis.net/profiles/eoc/dockerizedApplication"
}
EOF
```

<details>

<summary>Expected Response</summary>

```shell
{
  "deploymentResult": {
    "processSummary": {
      "abstract": "l1b_pge_cwl",
      "id": "l1b-cwl",
      "jobControlOptions": [
        "async-execute"
      ],
      "keywords": [],
      "processDescriptionURL": "http://127.0.0.1:5000/processes/l1b-cwl:develop",
      "title": "l1b_pge_cwl",
      "version": "develop"
    }
  }
}
```

</details>

* Check the registered processes again, you should see the newly submitted `l1b-cwl` process:

```shell
curl -s "http://${WPST_API}:5001/processes" |  jq
```

<details>

<summary>Expected Response</summary>

```json
{
    "processes": [
        {
            "abstract": "l1b_pge_cwl",
            "executionUnit": "docker.registry/ndvims:latest",
            "id": "l1b-cwl:develop",
            "immediateDeployment": "true",
            "jobControlOptions": [
                "async-execute"
            ],
            "keywords": "",
            "outputTransmission": [
                "reference"
            ],
            "owsContextURL": "https://github.com/unity-sds/unity-sps-workflows/blob/main/sounder_sips/ssips_L1b_workflow.cwl",
            "processVersion": "develop",
            "title": "l1b_pge_cwl"
        }
    ]
}
```

</details>

#### Step 3 - Check That the Newly Created `l1b-cwl` Image Was Uploaded to GHCR

<details>

<summary><a href="https://github.com/orgs/unity-sds/packages/container/package/unity-sps-prototype%2Fl1b-cwl">Link to GHCR</a></summary>

<img src="../../../../.gitbook/assets/image (2).png" alt="" data-size="original">

</details>

#### Step 4 - Trigger Execution of the `l1b-cwl` Process Through the WPS-T API

* This request will trigger the running Verdi container to pull and run the `l1b-cwl` Docker image from GHCR.

```shell
curl -0 -s -D - -o /dev/null "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
  "mode": "async",
  "response": "document",
    "inputs":[
    {
        "id": "input_collection_id",
        "data": "SNDR_SNPP_ATMS_L1A___1"
    },
    {
        "id": "start_datetime",
        "data": "2016-01-14T08:00:00Z"
    },
    {
        "id": "stop_datetime",
        "data": "2016-01-14T11:59:59Z"
    },
    {
        "id": "output_collection_id",
        "data": "SNDR_SNPP_ATMS_L1B_OUTPUT___1"
    }

    ],
    "outputs": [
    {
      "id": "output",
      "transmissionMode": "reference"
    }
    ] 
}
EOF
```

<details>

<summary>Expected Response Header</summary>

```shell
HTTP/1.1 201 CREATED
Server: Werkzeug/2.2.2 Python/3.8.10
Date: Mon, 07 Nov 2022 23:23:20 GMT
Content-Type: application/json
Content-Length: 3
code: 201
location: http://127.0.0.1:5000/processes/l1b-cwl:develop/jobs/faddc45e-3d2f-4b29-837e-f85ca3cf9783
ContentType: application/json
Connection: close
```

</details>

#### Step 5 - Check Job Status Through the WPS-T API

* Check the status of the job by it's ID:

```shell
JOB_ID={Find in the `location` field in above response header}
watch -n 5 "curl -s "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs/${JOB_ID}" | jq"
```

<details>

<summary>Expected Response</summary>

```shell
{
  "jobID": "90cba99f-b980-4d7b-b6ff-7eb943dda88f",
  "message": "Status of job 90cba99f-b980-4d7b-b6ff-7eb943dda88f",
  "status": "succeeded"
}
```

</details>

* Continuously monitor the status of the job by it's ID:

```shell
watch -n 5 "curl -s "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs/${JOB_ID}" | jq"
```

<details>

<summary>Expected Response</summary>

```shell
Every 5.0s: curl -s http://a71a54...  MT-315710: Mon Nov  7 17:20:10 2022

{
  "jobID": "68066f70-85f1-4894-9545-8791a1945842",
  "message": "Status of job 68066f70-85f1-4894-9545-8791a1945842",
  "status": "running" # monitor until completion (succeeded/failed)
}

```

</details>

* Check the status of all submitted `l1b-cwl:develop` jobs:

```shell
curl -s "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "jobs": [
    {
      "inputs": [],
      "jobID": "5e45d1cc-8106-45e1-a866-e2e715360f1e",
      "status": "succeeded"
    },
    {
      "inputs": [],
      "jobID": "cfa264d6-c639-4cef-b2d1-9cc5d02f06a1",
      "status": "succeeded"
    },
    {
      "inputs": [],
      "jobID": "90cba99f-b980-4d7b-b6ff-7eb943dda88f",
      "status": "succeeded"
    }
  ]
}
```

</details>

#### Step 6. - Check the UDS Output Data Collection for the Executed L1B PGE

```shell
aws s3 ls s3://uds-dev-cumulus-staging/SNDR_SNPP_ATMS_L1B_OUTPUT___1: --human-readable --recursive
```

<details>

<summary>Expected Response</summary>

```shell
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file02/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file02.cmr.xml
2022-11-07 17:18:33    5.6 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file02/test_file02.nc
2022-11-07 17:18:33    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file02/test_file02.nc.cas
2022-11-07 17:19:13    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file03/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file03.cmr.xml
2022-11-07 17:18:33    5.3 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file03/test_file03.nc
2022-11-07 17:18:33    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file03/test_file03.nc.cas
2022-11-07 17:19:11    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file04/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file04.cmr.xml
2022-11-07 17:18:33    5.5 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file04/test_file04.nc
2022-11-07 17:18:34    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file04/test_file04.nc.cas
2022-11-07 17:19:17    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file05/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file05.cmr.xml
2022-11-07 17:18:34    5.3 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file05/test_file05.nc
2022-11-07 17:18:34    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file05/test_file05.nc.cas
2022-11-07 17:19:12    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file06/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file06.cmr.xml
2022-11-07 17:18:34    5.3 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file06/test_file06.nc
2022-11-07 17:18:34    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file06/test_file06.nc.cas
2022-11-07 17:19:14    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file07/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file07.cmr.xml
2022-11-07 17:18:34    5.3 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file07/test_file07.nc
2022-11-07 17:18:34    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file07/test_file07.nc.cas
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file08/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file08.cmr.xml
2022-11-07 17:18:35    5.7 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file08/test_file08.nc
2022-11-07 17:18:35    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file08/test_file08.nc.cas
2022-11-07 17:19:14    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file09/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file09.cmr.xml
2022-11-07 17:18:35    5.7 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file09/test_file09.nc
2022-11-07 17:18:35    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file09/test_file09.nc.cas
2022-11-07 17:19:13    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file10/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file10.cmr.xml
2022-11-07 17:18:35    5.4 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file10/test_file10.nc
2022-11-07 17:18:35    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file10/test_file10.nc.cas
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file11/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file11.cmr.xml
2022-11-07 17:18:36    5.5 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file11/test_file11.nc
2022-11-07 17:18:36    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file11/test_file11.nc.cas
2022-11-07 17:19:16    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file12/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file12.cmr.xml
2022-11-07 17:18:36    5.8 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file12/test_file12.nc
2022-11-07 17:18:36    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file12/test_file12.nc.cas
2022-11-07 17:19:12    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file13/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file13.cmr.xml
2022-11-07 17:18:36    5.8 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file13/test_file13.nc
2022-11-07 17:18:37    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file13/test_file13.nc.cas
2022-11-07 17:19:14    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file14/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file14.cmr.xml
2022-11-07 17:18:37    5.7 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file14/test_file14.nc
2022-11-07 17:18:37    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file14/test_file14.nc.cas
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file15/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file15.cmr.xml
2022-11-07 17:18:37    5.9 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file15/test_file15.nc
2022-11-07 17:18:37    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file15/test_file15.nc.cas
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file16/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file16.cmr.xml
2022-11-07 17:18:37    5.9 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file16/test_file16.nc
2022-11-07 17:18:38    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file16/test_file16.nc.cas
2022-11-07 17:19:15    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file17/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file17.cmr.xml
2022-11-07 17:18:38    5.7 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file17/test_file17.nc
2022-11-07 17:18:38    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file17/test_file17.nc.cas
2022-11-07 17:19:16    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file18/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file18.cmr.xml
2022-11-07 17:18:38    5.6 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file18/test_file18.nc
2022-11-07 17:18:38    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file18/test_file18.nc.cas
2022-11-07 17:19:16    1.7 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file19/SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file19.cmr.xml
2022-11-07 17:18:38    5.3 MiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file19/test_file19.nc
2022-11-07 17:18:38    3.8 KiB SNDR_SNPP_ATMS_L1B_OUTPUT___1:test_file19/test_file19.nc.cas
```

</details>

#### Step 7 (Optional) - Un-deploy the `l1b-cwl` Process Through the WPS-T API

* Note this optional step is useful if debugging a failed job.

```shell
curl -s -X DELETE "http://${WPST_API}:5001/processes/l1b-cwl:develop" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "undeploymentResult": {
    "abstract": "l1b_pge_cwl",
    "executionUnit": "docker.registry/ndvims:latest",
    "id": "l1b-cwl:develop",
    "immediateDeployment": "true",
    "jobControlOptions": [
      "async-execute"
    ],
    "keywords": "",
    "outputTransmission": [
      "reference"
    ],
    "owsContextURL": "https://raw.githubusercontent.com/unity-sds/unity-sps-workflows/main/sounder_sips/ssips_L1b_workflow.cwl",
    "processVersion": "develop",
    "title": "l1b_pge_cwl"
  }
}
```

</details>

