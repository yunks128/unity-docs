---
description: >-
  New users (projects) of MDPS need to have their AWS account setup, users
  setup, and linked together with a MDPS shared services account.  This
  procedure outlines the steps needed to accomplish this.
---

# Project Onboarding Procedure

**PREREQUISITE**:  The [Shared Services, account must be setup](../developer-docs/common-services/docs/users-guide/deployment/shared-services-deployment.md)

### Venue Account Onboarding Procedure:

1. Project **obtains their own AWS account** (Bring your own Account).\

2. Project **agrees to EC2 conditions** (EULA / FIPS) on their new account\

3.  Project **notifies MDPS Team** that they want to onboard to Unity by creating a "New Project Venue Request" issue in [this MDPS repository](https://github.com/unity-sds/issue-triage/issues/new/choose):

    <figure><img src="../.gitbook/assets/Screenshot 2024-08-06 at 1.08.14 PM (1).png" alt=""><figcaption></figcaption></figure>
4.  **Project lets MDPS Team know what the set of project users are,** by creating "New User Request" issue(s) in [this MDPS repository](https://github.com/unity-sds/issue-triage/issues/new/choose):

    <figure><img src="../.gitbook/assets/Screenshot 2024-08-06 at 2.14.27 PM.png" alt=""><figcaption></figcaption></figure>
5.  **MDPS Team sets up initial set of users/roles** (manually for now):

    * MDPS Team **creates Cognito user groups** (roles)
      * `Unity-<PROJECT>-<VENUE>-ManagementConsole-ReadOnly`
      * `Unity-<PROJECT>-<VENUE>-ManagementConsole-Admin`
      * `Unity_Viewer`
      * `Unity_Admin`
      * The Cognito user group naming convention is available at [Cognito User Group Standards](../developer-docs/common-services/docs/users-guide/security/cognito-user-group-standards.md)
    * MDPS Team **creates Cognito users**
      * Each user should be created in the Shared Services Cognito user pool
      * Each user should be assigned these Groups at a minimum:
        * `Unity-<PROJECT>-<VENUE>-ManagementConsole-ReadOnly`
        * `Unity_Viewer`
      * The Cognito user naming convention is available at [Cognito User Standards](../developer-docs/common-services/docs/users-guide/security/cognito-user-standards.md)
      * Unity team collects user names (preferably the NASA AUIDs) and a valid email addresses for  users to create Cognito users
      * After creating the user in the Cognito user pool, the user receives a temporary password through email with instructions to change the password


6. **MDPS team adds project AWS account to shared service Resource Access Manager** (RAM) to enable sharing of SSM parameters. See [shared-services-deployment.md](../developer-docs/common-services/docs/users-guide/deployment/shared-services-deployment.md "mention") for more instructions.\

7. **In Project Venue Account:** **MDPS Team requests** wildcard cert for the venue, must add the cname record/value to the SHARED SERVICES DNS to approve its creation. [See instructions here](https://app.gitbook.com/s/cUYkPD7kBe7iT1LABkPZ/tips-and-tricks/speed-up-with-quick-find).\

8.  **MDPS Team sets up roles on account**:

    * Export short-term access keys for account on command-line
    * execute the [create roles script](https://github.com/unity-sds/unity-cs-infra/blob/main/aws\_role\_create/create\_roles\_and\_policies.sh)


9. **MDPS Team sets up the venue bastion host**
   * Creates an EC2 bastion host in project AWS account, which is able to deploy Management Console EC2.
   *   Create EC2 instance with the following configuration:

       * **Name of instance:**
         * Use the format `unity-<PROJECT>-<VENUE>-cs-management_console-bastion`
       * **AMI / instance type**:&#x20;
         * Get the AMI ID to use, by opening another tab, and copying the AMI specified in the `/mcp/amis/ubuntu2004-cset` SSM param
         * Go to "My AMIs" --> "Shared With Me" --> enter AMI ID in the drop-down text box
         * use a `t2.micro` instance
       * **Key Pair:**&#x20;
         * If a key pair doesn't already exist, create one in the format `unity-<PROJECT>-<VENUE>-bastion-pem` (do this in another tab first)
         * select keypair (use "Select Existing Keypair") to use (create a new one and save it for future use)
       * **Security Group:**&#x20;
         * If an existing `mc-bastion-sg` security doesn't already exist, then create one. It should have:
           * INCOMING CONNECTIONS:
             * none
           * OUTGOING CONNECTIONS:
             * open custom TCP for 443 to anywhere, and 80 to anywhere
         * Select the `mc-bastion-sg` security group.
       * **Networking:**
         * Make sure to select a public subnet (under the VPC setting)
       * Under Advanced, select an IAM Instance Profile of `Unity-CS_Service_Role-instance-profile`
       * launch instance
       * Connect to instance
       * `sudo su - ubuntu`
       * `git clone https://github.com/unity-sds/unity-cs-infra.git`
       * Back in the AWS console, create an image (AMI) from the EC2, to have as a backup.


10. **MDPS Team (or Project Team) deploys the Venue Infrastructure (Networking stack, Management Console, and more)**
    * connect to instance via AWS SSM connection
    * `sudo su - ubuntu`
    * `cd unity-cs-infra/nightly_tests ; git pull`
    * `./run.sh --destroy false --run-tests false --project-name <PROJECT> --venue-name <VENUE>`
    * Make sure to copy the URL of the Management Console that gets printed to the console, as part of running the above command.  If any issues or errors encountered, see below "Debugging Management Console" section.
    *   OPTIONAL STEPS IF YOU NEED TO DESTROY MANAGEMENT CONSOLE:

        * Run the following on the bastion host:
        * `./destroy.sh --project-name <PROJECT> --venue-name <VENUE>`


11. **Configure Shared Services HTTPD server to route to Venue "entry" ALB.**
    1. See steps [here](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/updating-venue-deployment).\

12. **Project User** (the one that has the `Unity-<PROJECT>-<VENUE>-ManagementConsole-Admin` role) **logs into Management Console** via CloudFront.   Use the URL that was copied above.\

13. **Project user runs Core Setup actions in Management Console**\

14. **Project User deploys further marketplace items**



## Debugging Issues with the Management Console

1\) SSH into Management Console EC2 instance

2\) `cd /var/log/`

3\) `cat managementconsole.log`
