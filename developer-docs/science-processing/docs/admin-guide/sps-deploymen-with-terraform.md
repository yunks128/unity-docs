# 🧱 SPS Deploymen with Terraform

This page contains instructions on how to deploy the Unity Science Process System (SPS) to an AWS account managed by the NASA Mission Cloud Platform (MCP). The instructions describe creating a new SPS instance from scratch, storing the resulting Terraform state on an S3 back-end. Migrating the state of an existing SPS instance from the local storage to S3 is also possible, but not covered here.

The SPS deployment process consists of 3 steps:

* Deploy an EKS cluster
* Deploy Karpenter onto the EKS cluster
* Deploy Airflow onto the EKS cluster

### Pre-Requisites:

* Access to an MCP account (aka a 'venue')
* The following tools installed on the personal laptop:
  * The [AWS CLI](https://aws.amazon.com/cli/) tool
  * [Terraform](https://www.terraform.io/) version 1.4.6
  * The Kubernetes client [kubectl](https://kubernetes.io/docs/reference/kubectl/)

### Step 0: Setup the environment

* Checkout the source code repository. Note: it is highly recommended to start from a clean version of the "unity-sps" repository, so that the Terraform workspace is clean and set to "default".
  * git clone https://github.com/unity-sds/unity-sps.git
  * cd unity-sps
* Generate AWS short-term keys for the MCP venue of choice and add them to \~/.aws/credentials. For example:
  * mcp-venue-dev
  * mcp-venue-test
* Add the AWS keys to the environment:
  * export AWS\_REGION=us-west-2
  * export AWS\_PROFILE=XXXXXXXXXXXX\_mcp-tenantOperator
* Define additional environment variables that identify the SPS cluster to be deployed:
  * export PROJECT=\<project> (example: "unity")
  * export SERVICE\_AREA=sps
  * export VENUE=\<venue> (example: "dev")
  * export DEPLOYMENT=\<deployment> (examples: "luca", "nightly")
  * export COUNTER=\<counter> (examples: "1", "2", ...)

### Step 1: Provision an Elastic Kubernetes Service (EKS) Cluster on MCP

* Setup an additional environmental variable that defines the component to be deployed, in this case EKS:
  * export COMPONENT=eks
* Define variables to configure the Terraform S3 backend:
  * export BUCKET=\<bucket> (example: "unity-unity-dev-bucket")
    * This is the S3 bucket where the SPS Terraform state will be stored - it is different for each venue
  * export KEY=sps/tfstates/${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}-${DEPLOYMENT}-${COUNTER}.tfstate
    * This is the specific path to the Terraform state on S3 for this SPS component. This key is uniquely identified by the environmental vaiables previously set. Note that that all state files will be stored inside a common folder "sps/tfstates".
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
