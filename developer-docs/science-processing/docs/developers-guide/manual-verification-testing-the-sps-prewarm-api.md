# Manual Verification: Testing the SPS Prewarm API

### Step 1 (optional) - Deploy a U-SPS Cluster in the MCP Dev Account

* **Note:** You can skip this step if you're using a cluster that has already been deployed. You'll just need the URL of the APIs.

```sh
(local-machine) $ cd unity-sps-prototype/terraform-unity
(local-machine) $ terraform apply -var-file=MCP_DEV.tfvars -no-color 2>&1 --auto-approve | tee apply_output.txt
# Get the hostnames of the load balancers
(local-machine) $ terraform output
load_balancer_hostnames = {
  "ades_wpst" = "ac9b98e5058a140dba79943df80e9b48-751783497.us-west-2.elb.amazonaws.com"
  "grq_es" = "a21818cae05464be387250b577c80aab-553613407.us-west-2.elb.amazonaws.com"
  "mozart_es" = "ab5455b97a3904760ae865a197a26806-375650635.us-west-2.elb.amazonaws.com"
  "sps_api" = "a06e57779a2b441dcb46c546b59e327c-685374922.us-west-2.elb.amazonaws.com"
}
# Set the the value of SPS_API for future API requests
(local-machine) $ SPS_API=a06e57779a2b441dcb46c546b59e327c-685374922.us-west-2.elb.amazonaws.com
```

### Step 2 - Using the SPS API, check the current status of the Verdi node group

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

### Step 3 - Send a few prewarm request to change the desired size of the Verdi node group

#### Set the number of nodes to 7

```sh
curl -s -X POST "http://${SPS_API}:5002/sps/prewarm" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"num_nodes\":7}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "success": true,
  "message": "Prewarm request accepted with ID 473f0bc4-ff1b-456e-9ac4-7d904c29210d",
  "prewarm_request_id": "473f0bc4-ff1b-456e-9ac4-7d904c29210d"
}
```

</details>

#### Set the number of nodes to 6

```sh
curl -s -X POST "http://${SPS_API}:5002/sps/prewarm" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"num_nodes\":6}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "success": true,
  "message": "Prewarm request accepted with ID 7200853e-49f8-4d16-aa4a-048e45deb97f",
  "prewarm_request_id": "7200853e-49f8-4d16-aa4a-048e45deb97f"
}
```

</details>

#### Set the number of nodes to 5

```sh
curl -s -X POST "http://${SPS_API}:5002/sps/prewarm" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"num_nodes\":5}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "success": true,
  "message": "Prewarm request accepted with ID d5725491-5e5e-4caf-93d0-5dcd5fca73f7",
  "prewarm_request_id": "d5725491-5e5e-4caf-93d0-5dcd5fca73f7"
}
```

</details>

### Step 4 - Capture the prewarm update IDs from the responses

```sh
PREWARM_REQUEST_ID_1=473f0bc4-ff1b-456e-9ac4-7d904c29210d
PREWARM_REQUEST_ID_2=7200853e-49f8-4d16-aa4a-048e45deb97f
PREWARM_REQUEST_ID_3=d5725491-5e5e-4caf-93d0-5dcd5fca73f7
```

### Step 5 - Check the status of the node group's prewarm requests

#### The first request should be in the `Running` state

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/prewarm/${PREWARM_REQUEST_ID_1}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "status": "Running",
  "last_update_timestamp": "2023-04-03T23:31:56.544448",
  "num_nodes": 7,
  "ready_nodes": 3,
  "node_group_update": {
    "id": "0aef6adb-6efd-3292-9bf0-3ef988edde19",
    "status": "Successful",
    "type": "ConfigUpdate",
    "params": [
      {
        "type": "DesiredSize",
        "value": "7"
      }
    ],
    "createdAt": "2023-04-03T23:30:03.698000+00:00",
    "errors": []
  },
  "error": null
}
```

</details>

#### The second and third requests should be in the `Accepted` state

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/prewarm/${PREWARM_REQUEST_ID_2}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "status": "Accepted",
  "last_update_timestamp": "2023-04-03T23:31:02.858601",
  "num_nodes": 6,
  "ready_nodes": 3,
  "node_group_update": null,
  "error": null
}
```

</details>

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/prewarm/${PREWARM_REQUEST_ID_3}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "status": "Accepted",
  "last_update_timestamp": "2023-04-03T23:31:26.234374",
  "num_nodes": 5,
  "ready_nodes": 3,
  "node_group_update": null,
  "error": null
}
```

</details>

### Step 6 - Monitor the first request until the returned status is \`Successful\`

```sh
watch -n 5 "curl -s -X GET "http://${SPS_API}:5002/sps/prewarm/${PREWARM_REQUEST_ID_1}" | jq"
```

<details>

<summary>Expected Response</summary>

```json
Every 5.0s: curl -s -X GET http://a3d62cb3903c740389bb2ee3ba66c405-100...  MT-315710: Mon Apr  3 16:33:05 2023

{
  "status": "Running",
  "last_update_timestamp": "2023-04-03T23:33:03.184537",
  "num_nodes": 7,
  "ready_nodes": 3,
  "node_group_update": {
    "id": "0aef6adb-6efd-3292-9bf0-3ef988edde19",
    "status": "Successful",
    "type": "ConfigUpdate",
    "params": [
      {
        "type": "DesiredSize",
        "value": "7"
      }
    ],
    "createdAt": "2023-04-03T23:30:03.698000+00:00",
    "errors": []
  },
  "error": null
}
```

</details>

### Step 7 - Check the status of the Verdi node group after the first request has completed

```sh
curl -s -X GET "http://${SPS_API}:5002/sps/ready-nodes" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "ready_nodes": 7
}
```

</details>

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
  "desired_size": 7,
  "min_size": 0,
  "max_size": 10,
  "ready_nodes": 3
}
```

</details>

### Step 8 - Fail Gracefully

#### Send a prewarm request to set the number of nodes 10,000

```sh
curl -s -X POST "http://${SPS_API}:5002/sps/prewarm" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"num_nodes\":10000}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "message": "Requested number of nodes (10000) is larger than the node group's max size (10)"
}
```

</details>

#### Send a prewarm request to set the number of nodes to -1

```sh
curl -s -X POST "http://${SPS_API}:5002/sps/prewarm" -H "accept: application/json" -H "Content-Type: application/json" -d "{\"num_nodes\":-1}" | jq
```

<details>

<summary>Expected Response</summary>

```json
{
  "message": "Requested number of nodes (-1) is smaller than the node group's min size (0)"
}
```

</details>

