# 🧱 SPS Deployment with Terraform

This page contains instructions on how to deploy the Unity Science Process System (SPS) to an AWS account managed by the NASA Mission Cloud Platform (MCP). The instructions describe creating a new SPS instance from scratch, storing the resulting Terraform state on an S3 back-end. Migrating the state of an existing SPS instance from the local storage to S3 is also possible, but not covered here.

The SPS deployment process consists of 3 steps:

* Deploy an EKS cluster
* Deploy Karpenter onto the EKS cluster
* Deploy Airflow onto the EKS cluster

### Pre-Requisites:

* Access to an MCP account (aka a 'venue')
* The following tools installed on the personal laptop:
  * The [AWS CLI](https://aws.amazon.com/cli/) tool
  * [Terraform](https://www.terraform.io/) version 1.8.2
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

### Step 1: Provision an Elastic Kubernetes Service (EKS) Cluster on MCP

In this step, you will deploy a Kubernetes cluster onto the AWS infrastructure.

* cd unity-sps/terraform-unity/modules/terraform-unity-sps-eks
* Setup an additional environmental variable that defines the component to be deployed, in this case EKS:
  * export COMPONENT=eks
* Define variables to configure the Terraform S3 backend:
  * export BUCKET=\<bucket> (example: "unity-unity-dev-bucket")
    * This is the S3 bucket where the SPS Terraform state will be stored - it is different for each venue
  * export KEY=sps/tfstates/${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfstate
    * This is the specific path to the Terraform state on S3 for this SPS component. This key is uniquely identified by the environmental vaiables previously set. Note that that all state files will be stored inside a common folder "sps/tfstates".
* Deploy the cluster via Terraform:
  * cd unity-sps/terraform-unity/modules/terraform-eks-cluster
  * terraform init -reconfigure -backend-config="bucket=$BUCKET" -backend-config="key=$KEY"
  * Create a Terraform configuration file which, at a minimum, must contain values for all Terraform variables that do not have defaults:
    * mkdir -p tfvars
    * export TFVARS\_FILENAME=${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfvars
    * terraform-docs tfvars hcl . --output-file tfvars/${TFVARS\_FILENAME}
    * edit tfvars/${TFVARS\_FILENAME}
      * Remove the first and last comment lines containing "...TF\_DOCS..."
      * Insert the proper values for "project" and "venue".
      * You may leave all the other fields as they are
  * Warning: before starting the deployment, make sure the AWS temporary credentials are going to be valid for 30+ minutes - if in doubt, renew them to avoid deployment failure and an ensuing messy process of manual clean up
  * terraform apply --var-file=tfvars/${TFVARS\_FILENAME}
    * If everything looks good, type "yes" to start the deployment process, which will take 20-30 minutes
  * When the deployment completes successfully, create a Kubernetes file to interact with the EKS cluster:
    * export CLUSTER\_NAME=${PROJECT}-${VENUE}-sps-eks
    * aws eks update-kubeconfig --region us-west-2 --name $CLUSTER\_NAME --kubeconfig ./$CLUSTER\_NAME.cfg
    * export KUBECONFIG=$PWD/$CLUSTER\_NAME.cfg
    * echo $KUBECONFIG
  * Verify you can interact with the EKS cluster:
    * kubectl get all -A
* Later, after the EKS cluster is no more useful, it can be destroyed wit the following command:
  * terraform destroy--var-file=tfvars/${TFVARS\_FILENAME}"
    * If everything looks good, type "yes" to start the destruction process

### Step 2: Deploy Karpenter onto the EKS Cluster

In this step, you will use a Helm Chart to deploy the Karpenter controller onto the EKS cluster. Karpenter is a Kubernetes plugin to manage auto-scaling of EC2 nodes to execute workloads.

* cd unity-sps/terraform-unity/modules/terraform-unity-sps-karpenter
* Setup the environment to deploy Karpenter:
  * export COMPONENT=karpenter
  * export KEY=sps/tfstates/${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfstate
  * echo $KEY
  * Note: it is assumed all other environment variables defined above are still defined in the current environment
  * Warning: it is recommended to renew the AWS credentials before starting deployment
* Initialize Terraform for the new cluster and deployment:
  * terraform init -reconfigure -backend-config="bucket=$BUCKET" -backend-config="key=$KEY"
* Just like before, create a Terraform configuration file which, at a minimum, must contain values for all Terraform variables that do not have defaults:
  * mkdir -p tfvars
  * export TFVARS\_FILENAME=${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfvars
  * terraform-docs tfvars hcl . --output-file tfvars/${TFVARS\_FILENAME}
  * edit tfvars/${TFVARS\_FILENAME}
    * Remove the first and last comment lines containing "...TF\_DOCS..."
    * Insert the proper values for "project" and "venue".
    * You may leave all the other fields as they are
  * terraform apply --var-file=tfvars/${TFVARS\_FILENAME}
    * If everything looks good, type "yes" to start the deployment process, which will take 20-30 minutes
    * The Karpenter deployment should only take a few minutes
  * Later, to destroy the Karpenter infrastructure:
    * terraform destroy--var-file=tfvars/${TFVARS\_FILENAME}"
      * If everything looks good type "yes"

### Step 3: Deploy Airflow onto the EKS Cluster

In this step, you will deploy the Airflow orchestration engine using a Helm Chart onto the existing EKS cluster.

* cd unity-sps/terraform-unity
* Setup the environment to deploy Airflow:
* export COMPONENT=airflow
* export KEY=sps/tfstates/${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfstate
* echo $KEY
* Note: it is assumed all other environment variables defined above are still defined in the current environment
* Warning: it is recommended to renew the AWS credentials before starting deployment
* Initialize Terraform for the new cluster and deployment:
  * terraform init -reconfigure -backend-config="bucket=$BUCKET" -backend-config="key=$KEY"
* Create  Terraform configuration file for the Airflow component:
  * mkdir -p tfvars
  * export TFVARS\_FILENAME=${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}.tfvars
  * terraform-docs tfvars hcl . --output-file tfvars/${TFVARS\_FILENAME}
  * edit tfvars/${TFVARS\_FILENAME}
    * Just like before, remove the first/last comment lines and dd the values for "project" and "venue"
    * Also enter the values for "airflow\_webserver\_password" (choose one value) and "kubeconfig\_filepath" (enter the value of $KUBECONFIG)
  * Warning: it is recommended to rebnew the AWS credentials
  * terraform apply --var-file=tfvars/${TFVARS\_FILENAME}
    * If everything looks good, type "yes" to start the deployment process, which will take 20-30 minutes
    * The Airflow deployment should around 15-20 minutes
