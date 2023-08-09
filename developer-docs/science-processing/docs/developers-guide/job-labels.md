---
description: How to add labels to job execution requests.
---

# Job Labels

Job labels allow users to associate a job with an arbitrary set of strings. Jobs can searched by their labels in the job database.

### Job Requests

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

