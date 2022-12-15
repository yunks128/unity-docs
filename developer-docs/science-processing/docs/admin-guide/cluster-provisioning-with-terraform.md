---
description: Documentation for provisioning a U-SPS cluster on MCP using Terraform
---

# Cluster Provisioning with Terraform

### Development Workflow

#### Dev Requirements:

* [Terraform](https://learn.hashicorp.com/tutorials/terraform/install-cli)
* [tfenv](https://github.com/tfutils/tfenv) - Terraform version manager.
* [Pre-commit](https://pre-commit.com/) - Framework for managing and maintaining multi-language pre-commit hooks.
* [act](https://github.com/nektos/act) - Run Github Actions locally.
* [tflint](https://github.com/terraform-linters/tflint) - Terraform Linter.
* [terrascan](https://github.com/accurics/terrascan) - Static code analyzer for Infrastructure as Code.
* [tfsec](https://github.com/aquasecurity/tfsec) - Security scanner for Terraform code.
* [terraform-docs](https://github.com/terraform-docs/terraform-docs) - Generate documentation from Terraform modules.
* [Terratest](https://terratest.gruntwork.io) - Go library that provides patterns and helper functions for testing infrastructure, with 1st-class support for Terraform.

#### Auto-generate a terraform.tfvars template file:

```shell
$ cd terraform-unity
$ terraform-docs tfvars hcl .
```

```json
celeryconfig_filename  = "celeryconfig_remote.py"
datasets_filename      = "datasets.remote.template.json"
deployment_environment = "mcp"
docker_images = {
  "ades_wpst_api": "ghcr.io/unity-sds/unity-sps-prototype/ades-wpst-api:unity-v0.0.1",
  "busybox": "k8s.gcr.io/busybox",
  "hysds_core": "ghcr.io/unity-sds/unity-sps-prototype/hysds-core:unity-v0.0.1",
  "hysds_factotum": "ghcr.io/unity-sds/unity-sps-prototype/hysds-factotum:unity-v0.0.1",
  "hysds_grq2": "ghcr.io/unity-sds/unity-sps-prototype/hysds-grq2:unity-v0.0.1",
  "hysds_mozart": "ghcr.io/unity-sds/unity-sps-prototype/hysds-mozart:unity-v0.0.1",
  "hysds_ui": "ghcr.io/unity-sds/unity-sps-prototype/hysds-ui-remote:unity-v0.0.1",
  "hysds_verdi": "ghcr.io/unity-sds/unity-sps-prototype/hysds-verdi:unity-v0.0.1",
  "logstash": "docker.elastic.co/logstash/logstash:7.10.2",
  "mc": "minio/mc:RELEASE.2022-03-13T22-34-00Z",
  "minio": "minio/minio:RELEASE.2022-03-17T06-34-49Z",
  "rabbitmq": "rabbitmq:3-management",
  "redis": "redis:latest"
}
kubeconfig_filepath = ""
mozart_es = {
  "volume_claim_template": {
    "storage_class_name": "gp2-sps"
  }
}
namespace = ""
node_port_map = {
  "ades_wpst_api_service": 30011,
  "grq2_es": 30012,
  "grq2_service": 30002,
  "hysds_ui_service": 30009,
  "minio_service_api": 30007,
  "minio_service_interface": 30008,
  "mozart_es": 30013,
  "mozart_service": 30001,
  "rabbitmq_mgmt_service_cluster_rpc": 30003,
  "rabbitmq_service_cluster_rpc": 30006,
  "rabbitmq_service_epmd": 30004,
  "rabbitmq_service_listener": 30005,
  "redis_service": 30010
}
service_type = "LoadBalancer"
```

### Deploy the Cluster

This method will use Terraform to deploy the Kubernetes cluster represented by the `~/.kube/config` file which is referenced in `terraform-unity/main.tf`. Terraform will deploy the resources in the Kubernetes namespace named in `terrafrom/variables.tf` (defaults to `unity-sps`). Additional variables (including secrets) can be set in `terraform.tfvars`, a template is shown below.

From within the Terraform root module directory (`terraform-unity/`), run the following commands to initialize, and apply the Terraform module:

```bash
$ cd terraform-unity/
$ terraform init
$ terraform apply
```

### Teardown the Cluster

From within the Terraform root module directory (terraform-unity/), run the following command to destroy the SPS cluster:

```
$ terraform destroy
```

### Prior to pushing new changes to the repo, please ensure that you done have the following and the checks have passed:

1.  Run the pre-commit hooks. These hooks will perform static analysis, linting, security checks. The hooks will also reformat the code to conform to the style guide, and produce the auto-generated documentation of the Terraform module.

    ```shell
    # Run all hooks:
    $ pre-commit run --files terraform-modules/*
    $ pre-commit run --files terraform-unity/*

    # Run specific hook:
    $ pre-commit run <hook_id> --files terraform-modules/terraform-unity-sps-hysds-cluster/*.tf
    $ pre-commit run <hook_id> --files terraform-unity/*.tf
    ```
2.  Run the Github Actions locally. These actions include similar checks to the pre-commit hooks, however, the actions not have the ability to perform reformatting or auto-generation of documentation. This step is meant to mimic the Github Actions which run on the remote CI/CD pipeline.

    ```shell
    # Run all actions:
    $ act

    # Run specific action:
    $ act -j "<job_name>"
    $ act -j terraform_validate
    $ act -j terraform_fmt
    $ act -j terraform_tflint
    $ act -j terraform_tfsec
    $ act -j checkov

    # You may need to authenticate with Docker hub in order to successfully pull some of the associated images.
    $ act -j terraform_validate -s DOCKER_USERNAME=<insert-username> -s DOCKER_PASSWORD=<insert-password>
    ```
3.  Run the Terratest smoke test. At the moment, this represents a _**very**_ basic smoke test for our deployment which simply checks the endpoints of the various services.

    ```shell
    $ cd terraform-test
    $ go test -v -run TestTerraformUnity -timeout 30m | tee terratest_output.txt
    ```

### Debugging a Terraform Deployment

It is often useful to modify the level of TF\_LOG environment variable when debugging a Terraform deployment. The levels include: `TRACE`, `DEBUG`, `INFO`, `WARN`, and `ERROR`.

An example of setting the `TF_LOG` environment variable to `INFO`:

```bash
$ export TF_LOG=INFO
```

Additionally, it is also often useful to pipe the output of a Terraform deployment into a log file.

An example of piping the `terraform apply` output into a file named apply\_output.txt:

```bash
$ terraform apply -no-color 2>&1 | tee apply_output.txt
```

Occasionally, a Terraform deployment goes awry and Terraform loses track of existing resources. When this happens, `terraform destroy` is unable to clean up the resources and you'll likely end up with existing resource errors when attempting your next `terraform apply`. This requires some manual garbage collection of the lingering orphan resources. It also sometimes requires nuking the existing Terraform-related state tracking files/directory.

The following commands are useful for manually ensuring all orphan resources are destroyed:

```bash
$ helm uninstall mozart-es
$ helm uninstall grq-es
$ kubectl delete all --all -n unity-sps
$ kubectl delete cm --all -n unity-sps # deletes ConfigMap(s)
$ kubectl delete pvc --all -n unity-sps # deletes PersistentVolumeClaim(s)
$ kubectl delete pv --all -n unity-sps # deletes PersistentVolume(s)
$ kubectl delete namespaces unity-sps
$ rm -rf .terraform
$ rm terraform.tfstate
$ rm terraform.tf.backup
```

## Auto-generated Documentation of the Unity SPS Terraform Root Module

### Requirements

| Name      | Version  |
| --------- | -------- |
| terraform | >= 1.1.9 |

### Providers

No providers.

### Modules

| Name                    | Source                                                 | Version |
| ----------------------- | ------------------------------------------------------ | ------- |
| unity-sps-hysds-cluster | ../terraform-modules/terraform-unity-sps-hysds-cluster | n/a     |

### Resources

No resources.

### Inputs

| Name                    | Description                                                           | Type                                                                                                             | Default                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              | Required |
| ----------------------- | --------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------: |
| celeryconfig\_filename  | value                                                                 | `string`                                                                                                         | `"celeryconfig_remote.py"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                           |    no    |
| counter                 | value                                                                 | `number`                                                                                                         | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |    yes   |
| datasets\_filename      | value                                                                 | `string`                                                                                                         | `"datasets.remote.template.json"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                    |    no    |
| deployment\_environment | value                                                                 | `string`                                                                                                         | `"mcp"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                              |    no    |
| docker\_images          | Docker images for the Unity SPS containers                            | `map(string)`                                                                                                    | <pre><code>{  "ades_wpst_api": "ghcr.io/unity-sds/unity-sps-prototype/ades-wpst-api:unity-v0.0.1",  "busybox": "k8s.gcr.io/busybox",  "hysds_core": "ghcr.io/unity-sds/unity-sps-prototype/hysds-core:unity-v0.0.1",  "hysds_factotum": "ghcr.io/unity-sds/unity-sps-prototype/hysds-factotum:unity-v0.0.1",  "hysds_grq2": "ghcr.io/unity-sds/unity-sps-prototype/hysds-grq2:unity-v0.0.1",  "hysds_mozart": "ghcr.io/unity-sds/unity-sps-prototype/hysds-mozart:unity-v0.0.1",  "hysds_ui": "ghcr.io/unity-sds/unity-sps-prototype/hysds-ui-remote:unity-v0.0.1",  "hysds_verdi": "ghcr.io/unity-sds/unity-sps-prototype/hysds-verdi:unity-v0.0.1",  "logstash": "docker.elastic.co/logstash/logstash:7.10.2",  "mc": "minio/mc:RELEASE.2022-03-13T22-34-00Z",  "minio": "minio/minio:RELEASE.2022-03-17T06-34-49Z",  "rabbitmq": "rabbitmq:3-management",  "redis": "redis:latest"}
</code></pre> |    no    |
| kubeconfig\_filepath    | Path to the kubeconfig file for the Kubernetes cluster                | `string`                                                                                                         | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |    yes   |
| mozart\_es              | value                                                                 | <pre><code>object({    volume_claim_template = object({      storage_class_name = string    })  })
</code></pre> | <pre><code>{  "volume_claim_template": {    "storage_class_name": "gp2-sps"  }}
</code></pre>                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |    no    |
| namespace               | Namespace for the Unity SPS HySDS-related Kubernetes resources        | `string`                                                                                                         | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |    yes   |
| node\_port\_map         | value                                                                 | `map(number)`                                                                                                    | <pre><code>{  "ades_wpst_api_service": 30011,  "grq2_es": 30012,  "grq2_service": 30002,  "hysds_ui_service": 30009,  "minio_service_api": 30007,  "minio_service_interface": 30008,  "mozart_es": 30013,  "mozart_service": 30001,  "rabbitmq_mgmt_service_cluster_rpc": 30003,  "rabbitmq_service_cluster_rpc": 30006,  "rabbitmq_service_epmd": 30004,  "rabbitmq_service_listener": 30005,  "redis_service": 30010}
</code></pre>                                                                                                                                                                                                                                                                                                                                                                                                                                                                |    no    |
| service\_type           | value                                                                 | `string`                                                                                                         | `"LoadBalancer"`                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |    no    |
| venue                   | The MCP venue in which the cluster will be deployed (dev, test, prod) | `string`                                                                                                         | n/a                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                  |    yes   |

### Outputs

| Name                      | Description                     |
| ------------------------- | ------------------------------- |
| load\_balancer\_hostnames | Load Balancer Ingress Hostnames |
|                           |                                 |
