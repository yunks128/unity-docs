---
description: Documentation for deploying an Airflow-based U-SPS on MCP using Terraform
---

# 🚀 SPS Airflow Deployment with Terraform

## Prerequisites

* Access to an MCP account (aka a 'venue')
* An SPS EKS cluster deployed in the same MCP account you would like SPS Airflow deployed into. To do this, following the instructions in the [docs](sps-eks-cluster-provisioning-with-terraform.md).
* A customized SPS Airflow image with SPS DAGs baked into it. To build this image, following the instructions in the [docs](sps-airflow-custom-docker-image-build-instructions.md).
* The following tools installed on the personal laptop:
  * [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli) - Infrastructure-as-code tool.
  * [tfenv](https://github.com/tfutils/tfenv) - Terraform version manager.
  * [terraform-docs](https://github.com/terraform-docs/terraform-docs) - Auto-generate documentation and `tfvar` files from Terraform modules.
  * [Python](https://www.python.org/)

## Dependencies from the other Unity Service Areas

A successful deployment of SPS depends on the following items from other the other Unity Service Areas.

<table><thead><tr><th width="148">Service Area</th><th width="262">Description</th><th width="142">AWS Service</th><th>Naming Convention</th></tr></thead><tbody><tr><td>Common Services (CS)</td><td>An SSM parameter containing public and private subnet lists for the VPC that the SPS EKS cluster is deployed in.</td><td>AWS Systems Manager - SSM paramter.</td><td><code>/unity/cs/account/network/subnet_list</code></td></tr></tbody></table>

## Setup Instructions

### Clone the SPS repository

```sh
git clone https://github.com/unity-sds/unity-sps.git
```

### Configure Python environment

* This Python environment will be used for executing tests.
*   From the root of the repository, create a Python virtualenv:

    ```sh
    python -m virtualenv venv
    ```
*   Install the required Python dependencies included in the `unity-sps` repo:

    ```sh
    source venv/bin/activate
    pip install -e ".[develop, test]"
    ```
*   Create a `.env` file for sensitive values used in the tests:

    ```sh
    touch .env
    ```
*   The `.env` should contain the following:

    ```sh
    # The password you would like to use for accessing Airflow,
    # this should match the value specified in your tfvars used to deploy Airflow
    AIRFLOW_WEBSERVER_PASSWORD=
    ```

### Configure the Terraform Workspace and prepare a `tfvars` File

1. If using `tfenv`, create a `.terraform-version` file at the root of the repo and insert the required Terraform version that is specified in `versions.tf`.
2.  Ensure the Terraform version you are using is equal to the value specified in `versions.tf`:

    ```sh
    terraform --version
    ```
3. Auto-generate a tfvars template file using `terraform-docs`. After the auto-generated tfvars template files are created, **certain values will need to be specified manually.** The values which will need to be specified manually are tagged with comments in the example auto-generated tfvars provided below:
   *   From the root of the repository, execute the following commands:

       ```sh
       venue=INSERT-VENUE # It’s just recommended to have this value match the counter included in the EKS cluster name. This will help you/others identify resources that are a part of the same SPS system.
       developer=INSERT-JPL-USERNAME # It’s just recommended to have this value match the counter included in the EKS cluster name. This will help you/others identify resources that are a part of the same SPS system.
       counter=INSERT-COUNTER # It’s just recommended to have this value match the counter included in the EKS cluster name. This will help you/others identify resources that are a part of the same SPS system.
       tfvars_filename=unity-${venue}-sps-${developer}-${counter}.tfvars

       cd terraform-unity
       mkdir tfvars
       terraform-docs tfvars hcl . --output-file "tfvars/${tfvars_filename}"
       ```
   * Manually override the following values in the auto-generated tfvars file:
   *   **Note:** The `BEGIN_TF_DOCS` and `END_TF_DOCS` tags will need to be removed from the tfvars file.

       ```sh
       <!-- BEGIN_TF_DOCS -->
       airflow_webserver_password = "" # The password you would like to use for accessing Airflow
       counter                    = "" # It’s recommended to have this value match the counter included in the EKS cluster name. This will help you/others identify resources that are a part of the same SPS system.
       custom_airflow_docker_image = {
         "name": "ghcr.io/unity-sds/unity-sps/sps-airflow",
         "tag": "develop" # Set this to the value you used when you built a custom SPS Airflow image
       }
       eks_cluster_name = "" # The EKS cluster which you are deploying Airflow into
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
       kubeconfig_filepath = "../k8s/kubernetes.yml" # The path to the kubeconfig which corresponds to the EKS cluster which you are deploying Airflow into 
       project             = "unity"
       release             = "" # The current release/sprint you are deploying for, e.g. 24.1
       service_area        = "sps"
       venue               = "" # The MCP venue which you are deploying into. It’s recommended to have this value match the counter included in the EKS cluster name. This will help you/others identify resources that are a part of the same SPS system.
       <!-- END_TF_DOCS -->
       ```

## Resource provisioning with Terraform

1.  Run a Terraform init:

    ```sh
    cd terraform-unity
    terraform init
    ```
2.  Run a Terraform plan:

    ```sh
    terraform plan -var-file=tfvars/${tfvars_filename}
    ```
3.  If you are satisfied with the output of the Terraform plan, run a Terraform apply to provision the cluster:

    ```sh
    terraform apply -var-file=tfvars/${tfvars_filename}
    ```
4.  If you are done using the resources deployed by the `terraform apply`, destroy the resources using the `terraform destroy` command:

    ```sh
    terraform destroy -var-file=tfvars/${tfvars_filename}
    ```

## Retrieve the endpoint for the Airflow UI

```bash
terraform output
load_balancer_hostnames = {
  "airflow" = "http://k8s-airflow-airflowi-52301ddb8d-1489339230.us-west-2.elb.amazonaws.com:5000"
}
```

## Smoke test the SPS Airflow deployment

From the root of the repository, execute the following commands:

```sh
source venv
cd unity-test
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



