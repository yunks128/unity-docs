---
description: Documentation for the SPS API
---

# Unity SPS API

The SPS API allows clients to manage the configuration and resource allocation of an already deployed Unity SPS system.

Currently, the SPS API is implemented by a [FastAPI](https://fastapi.tiangolo.com/) web server running as a deployment on the SPS kubernetes cluster. The endpoints will be exposed through the project API Gateway, but are currently exposed through AWS Elastic Load Balancers. Thus the domain of the service changes with deployment and clients will need to look it up in AWS before use.

## API Definition

### Get the Current Number of Worker Nodes

{% swagger src="../../../../.gitbook/assets/openapi.json" path="/sps/ready-nodes" method="get" %}
[openapi.json](../../../../.gitbook/assets/openapi.json)
{% endswagger %}

#### Example Request

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/ready-nodes" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "ready_nodes": 3
}
```

</details>

### Get Information about the Node Group's Configuration

{% swagger src="../../../../.gitbook/assets/openapi.json" path="/sps/node-group-info" method="get" %}
[openapi.json](../../../../.gitbook/assets/openapi.json)
{% endswagger %}

#### Example Request

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/node-group-info" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "instance_types": [
    "m3.medium"
  ],
  "desired_size": 3,
  "min_size": 0,
  "max_size": 10,
  "ready_nodes": 3
}
```



</details>

### Create Prewarm Request

The **Create Prewarm Request** is used to initialize an increase of the SPS compute infrastructure in anticipation of process executions.

{% swagger src="../../../../.gitbook/assets/openapi.json" path="/sps/prewarm" method="post" %}
[openapi.json](../../../../.gitbook/assets/openapi.json)
{% endswagger %}

#### &#x20;Example create prewarm request using curl:

```sh
curl -s -0 -X POST "http://${SPS_API}:5002/sps/prewarm" \
-H "Expect:" \
-H 'Content-Type: application/json; charset=utf-8' \
--data-binary @- << EOF | jq
{ 
"desired_size": 5
}
EOF
```

<details>

<summary>Expected Response</summary>

```json
{
  "success": true,
  "message": "Prewarm request accepted with ID 3122702a-8242-45ea-974d-b1ad5c33fe4a",
  "prewarm_request_id": "3122702a-8242-45ea-974d-b1ad5c33fe4a"
}
```

</details>

### Get Prewarm Request

The **Get Prewarm Request** is used to view the status of an already initialized prewarm.

{% swagger src="../../../../.gitbook/assets/openapi.json" path="/sps/prewarm/{prewarm_request_id}" method="get" %}
[openapi.json](../../../../.gitbook/assets/openapi.json)
{% endswagger %}

#### Example get prewarm request using curl:

```sh
curl -s "http://${SPS_API}:5002/sps/prewarm/${PREWARM_REQUEST_ID}" |  jq 
```

<details>

<summary>Expected Response</summary>

```json
{
  "status": "Running",
  "last_update_timestamp": "2023-03-29T21:32:33.066399",
  "desired_size": 1,
  "ready_nodes": 2,
  "node_group_update": {
    "id": "1c6aa0e8-197f-319f-9cc9-97b1639d5b3e",
    "status": "Successful",
    "type": "ConfigUpdate",
    "params": [
      {
        "type": "DesiredSize",
        "value": "1"
      }
    ],
    "createdAt": "2023-03-29T21:31:41.550000+00:00",
    "errors": []
  },
  "error": null
}
```

</details>

### Delete Prewarm Request (Not Currently Supported)

### Health Check Request

**Health Check** is a simple test endpoint to be used by deployment and integration tests to verify the liveness status of the SPS api service.

{% swagger src="../../../../.gitbook/assets/openapi.json" path="/sps/health-check" method="get" %}
[openapi.json](../../../../.gitbook/assets/openapi.json)
{% endswagger %}

#### Example echo request using curl:

```sh
curl -s "http://${SPS_API}:5002/sps/health-check" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
    "message": "The U-SPS On-Demand API is running and accessible"
}
```

</details>

