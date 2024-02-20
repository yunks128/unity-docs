---
description: Documentation for deploying an Airflow-based U-SPS on MCP using Terraform
---

# SPS Airflow Deployment with Terraform

## Prerequisites

* Access to an MCP account (aka a 'venue')
* An SPS EKS cluster deployed in the same MCP account you would like SPS Airflow deployed into. To do this, following the instructions in the [docs](sps-cluster-provisioning-with-terraform.md).
* The following tools installed on the personal laptop:
  * [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli) - Infrastructure-as-code tool.
  * [tfenv](https://github.com/tfutils/tfenv) - Terraform version manager.
  * [terraform-docs](https://github.com/terraform-docs/terraform-docs) - Auto-generate documentation and `tfvar` files from Terraform modules.

## Dependencies from the other Unity Service Areas

A successful deployment of SPS depends on the following items from other the other Unity Service Areas.

<table><thead><tr><th width="139">Service Area</th><th width="262">Description</th><th width="142">AWS Service</th><th>Naming Convention</th></tr></thead><tbody><tr><td>Common Services (CS)</td><td>An SSM parameter containing public and private subnet lists for the VPC that the SPS EKS cluster is deployed in.</td><td>AWS Systems Manager - SSM paramter.</td><td><code>/unity/cs/account/network/subnet_list</code></td></tr></tbody></table>

## Create a tfvars file to specify input variables

Input variables (including secrets) can be set in `terraform.tfvars`, a template can be auto-generated using `terraform-docs` as shown below.

```shell
cd terraform-unity
terraform-docs tfvars hcl . --output-file "terraform.tfvars"
```

```json
<!-- BEGIN_TF_DOCS -->
airflow_webserver_password = ""
counter                    = ""
custom_airflow_docker_image = {
  "name": "ghcr.io/unity-sds/unity-sps-prototype/sps-airflow",
  "tag": "develop"
}
eks_cluster_name = ""
helm_charts = {
  "airflow": {
    "chart": "airflow",
    "repository": "https://airflow.apache.org",
    "version": "1.11.0"
  },
  "keda": {
    "chart": "keda",
    "repository": "https://kedacore.github.io/charts",
    "version": "v2.13.1"
  }
}
kubeconfig_filepath = "../k8s/kubernetes.yml"
project             = "unity"
release             = ""
service_area        = "sps"
venue               = ""
<!-- END_TF_DOCS -->
```

## Deploy SPS Airflow

From within the Terraform root module directory (`terraform-unity/`), run the following commands to initialize, and apply the Terraform module:

```bash
cd terraform-unity/
terraform init
terraform apply
```

## Retrieve the endpoint for the Airflow UI

```bash
terraform output
load_balancer_hostnames = {
  "airflow" = "http://k8s-airflow-airflowi-52301ddb8d-1489339230.us-west-2.elb.amazonaws.com:5000"
}
```

## Smoke test the SPS Airflow deployment

```bash
AIRFLOW_ENDPOINT=http://k8s-airflow-airflowi-52301ddb8d-1489339230.us-west-2.elb.amazonaws.com:5000
pytest -s -vv --gherkin-terminal-reporter step_defs/test_airflow_api_health.py --airflow-endpoint $AIRFLOW_ENDPOINT
```

### Expected output

```bash
Feature: Airflow API health check
    Scenario: Check API health
        Given the Airflow API is up and running
        When I send a GET request to the health endpoint
        Then I receive a response with status code 200
        And each Airflow component is reported as healthy
        And each Airflow component's last heartbeat was received less than 30 seconds ago
    PASSED
=============================================== 1 passed in 0.37s ===============================================
```

## Teardown the Cluster

From within the Terraform root module directory (`terraform-unity/`), run the following command to destroy the SPS cluster:

```
terraform destroy
```

