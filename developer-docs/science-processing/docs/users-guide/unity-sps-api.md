---
description: Documentation for the SPS API
---

# Unity SPS API

The SPS API allows clients to manage the configuration and resource allocation of an already deployed Unity SPS system.

Currently, the SPS API is implemented by a [FastAPI](https://fastapi.tiangolo.com/) web server running as a deployment on the SPS kubernetes cluster. The endpoints will be exposed through the project API Gateway, but are currently exposed through AWS Elastic Load Balancers. Thus the domain of the service changes with deployment and clients will need to look it up in AWS before use.

## API Definition

### Create Prewarm Request

The **Create Prewarm Request** is used to initialize an increase of the SPS compute infrastructure in anticipation of process executions.

{% swagger src="../../../../.gitbook/assets/spsapi.json" path="/sps/prewarm" method="post" %}
[spsapi.json](../../../../.gitbook/assets/spsapi.json)
{% endswagger %}

#### &#x20;Example create prewarm request using curl:

```
curl -s -0 -X POST "http://${SPS_API}:5002/sps/prewarm" \                                                                                                                                                                                                              <aws:mcp-test>
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF | jq
{ 
"num_nodes" : 5     
}
EOF
```

<details>

<summary>Expected Response</summary>

```
{
  "success": true,
  "message": "Prewarm is not implemented, this request has no effect.",
  "request_id": "5"
}
```

</details>

### Get Prewarm Request

The **Get Prewarm Request** is used to view the status of an already initialized prewarm.

{% swagger src="../../../../.gitbook/assets/spsapi.json" path="/sps/prewarm/{request_id}" method="get" %}
[spsapi.json](../../../../.gitbook/assets/spsapi.json)
{% endswagger %}

#### Example get prewarm request using curl:

```
curl -s "http://${SPS_API}:5002/sps/prewarm/{reqid}" |  jq 
```

<details>

<summary>Expected Response</summary>

```
{
  "success": true,
  "message": "Status for prewarm request ID reqid.",
  "request_id": "reqid"
}
```

</details>

### Delete Prewarm Request

The **Delete Prewarm Request** is used to stop and revert an in progress prewarm request.

{% swagger src="../../../../.gitbook/assets/spsapi.json" path="/sps/prewarm/{request_id}" method="delete" %}
[spsapi.json](../../../../.gitbook/assets/spsapi.json)
{% endswagger %}

#### Example delete prewarm request using curl:

```
curl -s -X DELETE "http://${SPS_API}:5002/sps/prewarm/reqid" | jq
```

<details>

<summary>Expected Response</summary>

```
{
  "success": true,
  "message": "Prewarm request ID reqid deleted.",
  "request_id": "reqid"
}
```

</details>

### Echo Request

**Echo** is a simple test endpoint to be used by deployment and integration tests to verify the liveness status of the SPS api service.

{% swagger src="../../../../.gitbook/assets/spsapi.json" path="/test/echo" method="get" %}
[spsapi.json](../../../../.gitbook/assets/spsapi.json)
{% endswagger %}

#### Example echo request using curl:

```
curl -s "http://${SPS_API}:5002/test/echo" \                                                                                                                                                                                                                           <aws:mcp-test>
-H 'Content-Type: application/json;' \
--data-binary @- << EOF | jq
{ "echo_str" : "hello"
}
EOF
```

<details>

<summary>Expected Response</summary>

```
{
    "success": true,
    "message": "hello"
}
```

</details>
