---
description: Describe the various roles and policies are required for unity project account
---

# Necessary MCP Roles / Policies

## Management Console Deployment

The automatic deployment of services through the management console requires the creation of a service role through which AWS commands are executed.  In order to generate this role across multiple accounts in a consistent manner the creation of both the role and any policies connected to it have been written in a way that can be scripted and run from the command line.



\
Restrictions&#x20;
------------------

* Any roles created in the MCP environment must have a permission boundary set.  For our purposes we use the mcp-tenantOperator-AMI-APIG role as a permission boundary.  In other AWS environments this may not be necessary. &#x20;
* A maximum number of 10 policies may be assigned to each role in MCP.  This may seem like a lot, but 4 of those slots are taken by Amazon policies, and three of them by MCP policies. &#x20;
* There is a maximum character size of 6,144 characters per policy, in json form.  Due to the way the policy generator adds a new json array entry for each edit, this limit can fill up quite quickly.  Occasionally it may be necessary to manually edit and optimize the json
* In order to maintain a set of the least permissive policies there is a tool to generate a policy based on CloudTrail events.  Create a permissive policy, run a set of actions, and the least permission policy can be drawn from those actions.  This is not something that the Unity team can currently do on it's own, a request must be made to MCP in order to generate this restrictive policy.  This should be done when new policies are added.

\
Tools
-----

The only tools necessary to create the role and policies are the aws command line tools, and optionally the AWS Policy Generator.  This is done intentionally so that it may be easily integrated into other workflows.  \
\
When executed from the run.sh bash script, the process also makes use of the jq command line tool for parsing the output of certain AWS commands.

\
\
Steps to Implement
------------------

First and foremost the new role must be created.  The creation command includes the permission boundary and the new name.  This assumes that ${ROLE\_NAME} corresponds to a valid set of aws credentials and ${ACCOUNT\_NUMBER} is the AWS Account Number that matches those credentials.

```
aws iam create-role --role-name ${ROLE_NAME} --permissions-boundary arn:aws:iam::${ACCOUNT_NUMBER}:policy/mcp-tenantOperator-AMI-APIG --assume-role-policy-document file://U-CS_Service_Role_Trust_Policy.json --profile ${ACCOUNT_NAME}
```

Next the inline policy is attached to the role.  As this is a work in development, the final optimizations have not been done yet and we may find later that this step is unnecessary.  Right now it mirrors the MCP provided SSM role that allows us to connect to and EC2 instance through Session Manager, for troubleshooting purposes

```shellscript
aws iam put-role-policy --role-name ${ROLE_NAME} --policy-name U-CS_Minimum_ECS-Policy --policy-document file://Minimum_ECS_Policy.json --profile ${ACCOUNT_NAME}
```

Now the new policies need to be created so they can be attached to the new role.  These policies are specific to Unity, and all Unity-related permissions should be created in this way

```shellscript
aws iam create-policy --policy-name U-CS_Service_Policy --policy-document file://U-CS_Service_Policy.json --description "Policy containing permissions for automated U-CS Operations" --tags Key=ServiceArea,Value=U-CS --profile ${ACCOUNT_NAME}
aws iam create-policy --policy-name U-CS_Service_Policy_Ondemand --policy-document file://U-CS_Service_Policy_Ondemand.json --description "Policy containing additional permissions for automated U-CS Operations" --tags Key=ServiceArea,Value=U-CS --profile ${ACCOUNT_NAME}
```

Lastly, all of the policies that have been generated and all of the required pre-existing policies need to be attached to the role:

```shellscript
POLICY_LIST=(AmazonEC2ContainerRegistryPowerUser AmazonS3ReadOnlyAccess AmazonSSMManagedInstanceCore CloudWatchAgentServerPolicy DatalakeKinesisPolicy McpToolsAccessPolicy U-CS_Service_Policy U-CS_Service_Policy_Ondemand)
for POLICY_NAME in "${POLICY_LIST[@]}"
do
    POLICY_ARN=<get the policy arn>
    aws iam attach-role-policy --role-name ${ROLE_NAME} --policy-arn ${POLICY_ARN} --profile ${ACCOUNT_NAME}
done

```

And that's the new role and policies created.  This role can be attached to the ec2 instance that runs the management console now and will provide the console with the permissions to stand up all other services.\
\
TODO: Description of Required Permissions

This will be a list of all the permissions we required, taken from our optimized policies once we've narrowed them down\
