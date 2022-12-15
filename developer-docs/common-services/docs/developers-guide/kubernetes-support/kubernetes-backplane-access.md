# Kubernetes Backplane Access

Access to the backplane must be done via the kubectl CLI or other tooling like K9S which leverage the same API.

To gain access you must use your short term access keys available from [Kion](https://login.mcp.nasa.gov/portal).

The best way to configure kubectl for access is via the AWS CLI and the following command:

```shell
aws eks update-kubeconfig --region <region-code> --name <my-cluster>
```

The region code for default MCP based deployments is us-west-2 and the cluster name will be whatever was defined when the cluster was launched.

If you're authenticated against the correct AWS account at this point the aws command will write a kubeconfig file to your computer and you will be able to issue kubernetes commands.

To test this run:

`kubectl get ns`

this should return a list of existing namespaces in the kubernetes cluster

## Adding additional users

All users must have access the AWS account to gain access to the cluster. Once they have access you may grant additional users access via the eksctl cli tool.&#x20;

Here is an example:

```
eksctl create iamidentitymapping --cluster <my-cluster> --region=<region-code> --arn <user or role arn> --group system:masters --username admin
```
