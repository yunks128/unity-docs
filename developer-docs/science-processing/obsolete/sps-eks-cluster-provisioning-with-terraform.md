# 🧱 SPS EKS Cluster Provisioning with Terraform

This page contains instructions on how to deploy the Unity Science Process System (SPS) to an AWS account managed by the NASA Mission Cloud Platform (MCP).

### Pre-Requisites:

* Access to an MCP account (aka a 'venue')
* The following tools installed on the personal laptop:
  * The [AWS CLI](https://aws.amazon.com/cli/) tool
  * [Terraform](https://www.terraform.io/) version 1.4.6
  * The Kubernetes client [kubectl](https://kubernetes.io/docs/reference/kubectl/)

### Step 1: Provision an Elastic Kubernetes Service (EKS) Cluster on MCP

The EKS cluster can be deployed step-by-step using the following instructions:

* Generate AWS short-term keys for the MCP venue of choice and add them to \~/.aws/credentials. For example:
  * mcp-venue-dev
  * mcp-venue-test
* Set the shell environment accordingly.
  * export AWS\_REGION=us-west-2
  * export AWS\_PROFILE=XXXXXXXXXXXX\_mcp-tenantOperator
  * export VENUE="test"
    * or export VENUE="dev"
* Checkout the source code repository:
  * git clone https://github.com/unity-sds/unity-sps.git
* Choose a name for the EKS cluster to be deployed. For example:
  * export DEPLOYMENT\_NAME="myname"
* Set a simple counter variable so you don't have to search for the cluster name in the terraform output:
  * export COUNTER="aaa1" (or any other alphanumeric string)
* Deploy the cluster via Terraform:
  * cd unity-sps/terraform-unity/modules/terraform-eks-cluster
  * terraform init
  * terraform workspace new ${DEPLOYMENT\_NAME}
  * terraform apply -var "deployment\_name=${DEPLOYMENT\_NAME}" -var "counter=${COUNTER}" -var "venue=${venue}"
  * aws eks update-kubeconfig --region $AWS\_REGION --name "unity-${VENUE}-sps-eks-${DEPLOYMENT\_NAME}-${COUNTER}" --kubeconfig ./temp\_kube\_cfg
  * export KUBECONFIG=./temp\_kube\_cfg
  * kubectl get all -A
* Later, after the EKS cluster is no more useful, it can be destroyed wit the following command:
  * terraform destroy -var "deployment\_name=${DEPLOYMENT\_NAME}" -var "counter=${COUNTER}" -var "venue=${venue}"

Alternatively, the repository also contains a shell script that can be executed to test the creation, smoke-testing and destruction of an EKS cluster. To execute the script:

* cd unity-sps/terraform-unity/modules/terraform-eks-cluster
* terraform init
* ./validate.sh

The validate.sh script does the following:

* Generates a random alpha-numeric cluster name
* Creates a new Terraform workspace with the cluster name
* Runs 'terraform init' in the workspace
* Runs 'terraform apply -var "cluster\_name=random-cluster-name"'
* Initializes a kubeconfig for the cluster
* Verifies that the cluster is reachable with 'kubectl get all -A'
* Teardown the cluster with 'terraform destroy'

### Step 2: Deploy SPS with Airflow onto the EKS Cluster

Once the EKS cluster is up and running, the SPS system can be deployed onto it following these instructions.
