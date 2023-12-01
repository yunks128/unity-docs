# Detailed Breakdown of Project Onboarding Steps

<figure><img src="../../../../../../.gitbook/assets/SSM &#x26; Deployments Overview (2).png" alt=""><figcaption></figcaption></figure>

1\) Project **obtains their own AWS account** (Bring your own Account).

2\) Project **agrees to EC2 conditions** (EULA / FIPS) on their new account

3\) Project **notifies Unity team** that they want to onboard to Unity

4\) Project lets Unity know what the set of "starter" users are (name, email address, etc..)

5\) **Unity Team sets up initial set of users/roles** (manually for now):

1. Creates each user in Cognito
2. Each user gets a Unity-ManagementConsole-ReadOnly role
3. One or more users get the Unity-ManagementConsole-Admin role

6\) Project (or Unity personnel for now) launches PartnerSolution from project AWS account, which deploys Management Console EC2.

6a) Steps automated by CloudFormation:

1. Creates roles/policies (aws-role-create / run.sh) in project account
2. Creates ALB
3. Spins up Management Console EC2

6b) Steps automated as part of Management Console EC2 Boot Up:

1. Provisions ECS/httpd (and points it to ALB)
2. Connects shared services CloudFront to httpd Provision S3 bucket proj-level API Gateway ("/", parent placeholder for future routes)
3. Executes script to connect SS APIGW (proxy+) to proj-level
4. Deploys auth lambda code
5. Adds API GW authorizer
6. Writes out config file
7. Checks policies/perms
8. Creates sqlite DB

7\) Project User (the one that has the Unity-ManagementConsole-Admin role) logs into Management Console via CloudFront

8\) Project user runs Core Setup actions in Management Console

9\) Project User deploys further marketplace items
