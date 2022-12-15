# Kubernetes Security

To securely access AWS services via a Pod we currenty apply specific roles to the underlying EC2 servers are we are unable to manage the access to AWS via OIDC.

Our default rules are as follows:

...

To create your own permission set, you first need to create an IAM policy and role in the AWS console. Once you have done this you can specify the role in the EKS configuration to create an environment with your custom role.

### Pod level security

EKS does support pod level security via pod annotations and OIDC connectivity to the cluster. This is unavailable on the MCP cluster currently. If your EKS cluster is hosted elsewhere you may provide pod level security via the annoations.
