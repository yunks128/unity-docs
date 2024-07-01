# 🧱 Interacting with an Existing SPS Deployment

Administrators can interact with an existing SPS deployment (perhaps performed by another administrator) by following 3 steps. These steps must be executed separately for the three SPS components that need updating: eks, karpenter or airflow.

## Common Setup

* Define the AWS environmental variables for the desired venue:
  * export AWS\_REGION=us-west-2
  * export AWS\_PROFILE=XXXXXXXXXXXX\_mcp-tenantOperator
* Renew the AWS credentials for the appropriate MCP venue, adding them to \~/.aws/credentials
* Define environmental variables to point to the desired SPS deployment:
  * export PROJECT=\<project>&#x20;
  * export SERVICE\_AREA=sps
  * export VENUE=\<venue>
  * export DEPLOYMENT=\<deployment>
  * export COUNTER=\<counter>
  * export CLUSTER\_NAME=${PROJECT}-${VENUE}-sps-eks-${DEPLOYMENT}-${COUNTER}
    * Example: export CLUSTER\_NAME=unity-dev-sps-eks-nightly-2

## Step 1: Generate a new KUBECONFIG file

First, you must generate and reference a new KUBECONFIG file to point to the existing cluster, if such a file does not already exist on your system

* cd unity-sps/terraform-unity/modules/terraform-unity-sps-eks
* aws eks update-kubeconfig --region us-west-2 --name $CLUSTER\_NAME --kubeconfig ./$CLUSTER\_NAME.cfg
* export KUBECONFIG=$PWD/$CLUSTER\_NAME.cfg
* kubectl get all -A
* kubectl get pods -n airflow

Note: this is the only step needed if you only want to interact wit the existing cluster (like listing the deployed Pods), without altering its state.

## Step 2: Re-Initialize the Terraform State

You must re-initialize the Terraform state to reference the proper file on S3. This needs to happen separately for each component (eks, karpenter or airflow) that you want to update.

* cd unity-sps/terraform-unity/modules/terraform-unity-sps-eks
  * or cd unity-sps/terraform-unity/modules/terraform-unity-sps-karpenter
  * or cd unity-sps/terraform-unity
* export COMPONENT=eks
  * or export COMPONENT=karpenter
  * or export COMPONENT=airflow
* export BUCKET=\<bucket>
  * Example: export BUCKET=unity-unity-dev-bucket
* export KEY=sps/tfstates/${PROJECT}-${VENUE}-${SERVICE\_AREA}-${COMPONENT}-${DEPLOYMENT}-${COUNTER}.tfstate
* echo $KEY
  * Make sure that the value of $KEY is what you expect
* terraform init -reconfigure -backend-config="bucket=$BUCKET" -backend-config="key=$KEY"

## Step 3: Reference the proper Terraform config file

Terraform configuration files for the various clusters are checked-in into GitHub and available on the local system in the "tfvars/" sub-directory of the current component.

* export TFVARS\_FILENAME=\<existing desired Terraform config file in tfvars/ sub-directory>
  * If modifying the "airflow" component, the config filename must be edited to add the value of the "airflow\_webserver\_password" and update the local path of "kubeconfig\_filepath"
* To update the current deployment:
  * terraform apply --var-file=tfvars/${TFVARS\_FILENAME}
* To destroy the current deployment:
  * terraform apply --var-file=tfvars/${TFVARS\_FILENAME}

Note: when destroying the whole deployment, components must be destroyed in reverse order of creation, i.e.:

* First destroi airflow
* The destroy karpenter
* Finally destroy eks
