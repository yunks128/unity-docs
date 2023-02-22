# Deployment of On-Demand SPS

## Procedure for Manual Deployment of SPS and On-Demand API

```
1)	Log into MCP DEV AWS (via Kion) as tenant operator role
2)	Go to CloudFormation
3)	Create Stack, with new resources
4)	Select the "Upload a template file" radio button
5)	In a separate terminal window clone:  https://github.com/unity-sds/unity-on-demand-cloudformation
6)	Back in browser upload the templates/unity_deployer_instance-control_plane.yaml file
7)	Click "Next"
8)	Enter a unique Stack name
9)	Select the only VPC ID
10)	Select Unity-Dev-Pub-Subnet04
11)	Select key pair (create a new one if necessary)
12)	Paste in your personal access token
13)	Click Next
14)	Click Next
15)	Check the "I acknowledge that AWS CloudFormation might create IAM resources." Box
16)	Click Submit
17)	Monitor the stack creation in the CloudFormations->Stacks screen
```

## Destroy Instructions:  (NOT TESTED YET)

```
1) drain pods
2) delete default group node
3) delete cluster
4) go into S3 bucket, and delete objects
5) delete on-demand-api formation stack
6) delete test-on-demand 
```
