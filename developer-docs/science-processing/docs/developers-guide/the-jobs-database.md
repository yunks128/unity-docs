---
description: Contents, design, and interactions with the jobs database.
---

# The Jobs Database

## What is the Jobs Database

The jobs database is an Elasticsearch database that contains real-time information about jobs and their status. Jobs first enter the database when they're created through the WPS-T ADES server, and information like their status and the outputs they created are updated throughout the job's execution.

## Interacting With the Jobs Database

The jobs database is an Elasticsearch database that is completely exposed to the end-user. Users can interact with the jobs database exactly how they would interact with any Elasticsearch database - through the Elasticsearch HTTP API.\
\
All jobs are put in to the database under the **jobs** index. We recommend the following tools for making using Elasticsearch easy:

* [Elasticvue](https://elasticvue.com/) - visualize the contents of the database and send requests to the API
* [Elasticsearch API Client](https://www.elastic.co/guide/en/elasticsearch/client/index.html) - programatically query the database in your language of choice

## Fields in the Jobs Database

Every job in the Jobs Database has the following fields.

**id**\
The job's id as returned in the header of the WPS-T execute jobs response\
\
**status**\
The job's execution status - this is a string that can be set to anything by the jobs "Update Job" step that is explained in [How Jobs are Updated in the Database](the-jobs-database.md#how-jobs-are-updated-in-the-database). In the current implementation, jobs have of 1 of 4 statuses:

* submitted: the job was submitted to the WPS-T ADES server
* running: the job is executing the CWL workflow on a worker node
* succeeded: the job successfully executed CWL workflow on a worker node
* failed: the CWL workflow failed on the worker node OR the job failed to submit to the backend

**inputs**\
A map of strings to values corresponding to the inputs field of the job execution request.

```
"inputs": {
	"input_ephatt_collection_id": "L0_SNPP_EphAtt___1",
	"input_science_collection_id": "L0_SNPP_ATMS_SCIENCE___1",
	"output_collection_id": "SNDR_SNPP_ATMS_L1A_OUTPUT___1",
	"static_dir": {
		"class": "Directory",
		"path": "/tmp/SOUNDER_SIPS/STATIC_DATA"
	},
	"start_datetime": "2016-01-14T08:00:00Z",
	"stop_datetime": "2016-01-14T11:59:59Z"
}
```

**outputs**\
<mark style="color:orange;">**NOTE**</mark>: outputs are not yet implemented for jobs, this field will be an empty map until implemented\
A map of strings to values corresponding the outputs field of the job execution request, with the a URL for each output.

**labels**\
A list of strings corresponding to the labels field of the job execution request. More on labels in [Job Labels](the-jobs-database.md#job-labels).\
`"labels": ["pre-process", "test", "campaign-3"]`

**user-id**\
<mark style="color:orange;">**Not yet implemented**</mark>

**start-time**\
<mark style="color:orange;">**Not yet implemented**</mark>

**stop-time**\
<mark style="color:orange;">**Not yet implemented**</mark>

## How Jobs are Updated in the Database

A job is first added to the jobs database on a successful execute request to the WPS-T ADES server. When a job is first added it has status "submitted".

A job is updated from the worker node that is executing the job. Workflows that are sent to the SPS for execution contain steps for running an "Update Job" [CWL CommandLineTool](https://www.commonwl.org/v1.0/CommandLineTool.html) at the start and end of the workflow. Job update steps can be added in between other steps in a CWL to provide more granularity on the jobs status.

<figure><img src="../../../../.gitbook/assets/jobsdb.png" alt=""><figcaption></figcaption></figure>

Job updates are sent to an SQS queue via an SNS topic where they are consumed by a lambda function that adds them to the jobs database. Using a queue and asynchronous job database writing decouples job execution time from job database writes.

## Job Labels

Job labels allow users to associate a job with an arbitrary set of strings. Jobs can searched by their labels in the job database.

To create a job with labels simply add the field `labels` to the request body. The field `labels` is required to be an array of strings, and an empty array is treated the same as excluding field altogether.

```
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
    ],
    "labels": ["re-processing-3", "release23.3", "08-08-23"]
}
EOF
```

## Job Requests

As described in the [job execution tutorial](tutorial-execution-of-the-l1b-cwl-workflow-via-the-wps-t-api.md) - jobs are created by sending a POST request to the `/processes/{processId}:{processVersion}/jobs` endpoint.

```
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



