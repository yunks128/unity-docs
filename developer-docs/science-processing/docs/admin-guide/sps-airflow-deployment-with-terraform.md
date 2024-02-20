---
description: Documentation for deploying an Airflow-based U-SPS on MCP using Terraform
---

# SPS Airflow Deployment with Terraform

## Prerequisites

* Access to an MCP account (aka a 'venue')
* An SPS EKS cluster deployed in the same MCP account you would like SPS Airflow deployed into. To do this, following the instructions in the [docs](sps-cluster-provisioning-with-terraform.md).
* The following tools installed on the personal laptop:
  * The [AWS CLI](https://aws.amazon.com/cli/) tool
  * [Terraform](https://www.terraform.io/)
  * The Kubernetes client [kubectl](https://kubernetes.io/docs/reference/kubectl/)

## Dependencies from the other Unity Service Areas

A successful deployment of SPS depends on the following items from other the other Unity Service Areas.

<table><thead><tr><th width="139">Service Area</th><th width="262">Description</th><th width="142">AWS Service</th><th>Naming Convention</th></tr></thead><tbody><tr><td>Common Services (CS)</td><td>An SSM parameter containing public and private subnet lists for the VPC that the SPS EKS cluster is deployed in.</td><td>AWS Systems Manager - SSM paramter.</td><td><code>/unity/cs/account/network/subnet_list</code></td></tr></tbody></table>

## Create a tfvars file to specify input variables

Input variables (including secrets) can be set in `terraform.tfvars`, a template can be auto-generated using `terraform-docs` as shown below.

```shell
$ cd terraform-unity
$ terraform-docs tfvars hcl . --output-file "terraform.tfvars"
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
$ cd terraform-unity/
$ terraform init
$ terraform apply
```

### Teardown the Cluster

From within the Terraform root module directory (`terraform-unity/`), run the following command to destroy the SPS cluster:

```
$ terraform destroy
```

