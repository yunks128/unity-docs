# Detailed Breakdown of Project Onboarding Steps

1\) Project **obtains their own AWS account** (Bring your own Account).

2\) Project **agrees to EC2 conditions** (EULA / FIPS) on their new account

3\) Project **notifies Unity team** that they want to onboard to Unity

4\) **Project lets Unity know what the set of "starter" users are** (name, email address, etc..)

5\) **Unity Team sets up initial set of users/roles** (manually for now):

* Creates each user in Cognito
* Each user gets a `Unity-<PROJECT>-<VENUE>-ManagementConsole-ReadOnly` role
* One or more users get the `Unity-<PROJECT>-<VENUE>-ManagementConsole-Admin` role

6\) **Unity Team sets up roles on account**:

* Export short-term access keys for account on command-line
* execute the [create roles script](https://github.com/unity-sds/unity-cs-infra/blob/main/aws\_role\_create/create\_roles\_and\_policies.sh)

7\) **Unity Teams sets up the venue bastion host**

* Creates an EC2 bastion host in project AWS account, which is able to deploy Management Console EC2.
  * Create EC2 instance with the following configuration:
    * a t2.micro instance with the AMI specified in the /mcp/amis/ubuntu2004-cset SSM param
    * select keypair to use (create a new one and save it for future use)
    * select a standard security group that gives access on port 22.  Lock down to JPL source IPs
    * Make sure to put it in a public subnet (under the VPC setting)
    * Under Advanced, select an IAM Instance Profile of `Unity-CS_Service_Role-instance-profile`
    * launch instance

8\) **Unity Team deploys the Management Console**

* connect to instance via SSM connection (or SSH via pem file)
* `sudo su - ubuntu`
* `git clone https://github.com/unity-sds/unity-cs-infra.git`
* Back in the AWS console, create an image (AMI) from the EC2, to have as a backup.
* `cd unity-cs-infra/nightly_tests`
* `./run.sh --destroy false --run-tests false --project-name <PROJECT> --venue-name <VENUE>`
* Make sure to copy the URL of the Management Console that gets printed to the console, as part of running the above command.

OPTIONAL STEPS IF YOU NEED TO DESTROY MANAGEMENT CONSOLE:

* In the Management Console, click the "Uninstall" button
* Run the following on the bastion host:
  * `./destroy.sh --project-name <PROJECT> --venue-name <VENUE>`



9\) **Project User** (the one that has the `Unity-<PROJECT>-<VENUE>-ManagementConsole-Admin` role) **logs into Management Console** via CloudFront.   Use the URL that was copied above.

10\) **Project user runs Core Setup actions in Management Console**

11\) **Project User deploys further marketplace items**
