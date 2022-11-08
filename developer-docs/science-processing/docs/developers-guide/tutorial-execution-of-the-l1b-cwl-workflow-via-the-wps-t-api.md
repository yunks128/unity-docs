# Tutorial: Execution of the L1B CWL Workflow via the WPS-T API



#### Step 1 - Deploy a U-SPS Cluster in the MCP Dev Account

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

```shell
curl -0 -s -D - -o /dev/null "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF
{
  "mode": "async",
  "response": "document",
  "inputs": [
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
curl -s "http://${WPST_API}:5001/processes/l1b-cwl:develop/jobs/${JOB_ID}" | jq
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

Check the status of all submitted `l1b-cwl:develop` jobs:

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

#### Step 5b (Optional) - Un-deploy the `l1b-cwl` Process Through the WPS-T API

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
