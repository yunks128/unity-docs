---
description: Procedure for updating a venue deployment
---

# Updating Venue Deployment

1. Prerequsite:  Have a bastion host.  See [instructions here](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/deployment-concepts-and-infrastructure/detailed-breakdown-of-project-onboarding-steps), to create one, if one doesn't exist already.
2. Destroy Management Console via bastion host.  See [instructions here](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/deployment-concepts-and-infrastructure/detailed-breakdown-of-project-onboarding-steps).
3. Deploy new Management Console via bastion host.   See [instructions here](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/users-guide/deployment/deployment-concepts-and-infrastructure/detailed-breakdown-of-project-onboarding-steps).
4.  Update the link in the Shared Services HTTPD, to point to newly deployed venue ALB

    1. Log into venue account --> EC2 --> Load Balancers
    2. Obtain ALB URL from venue
       1. for example: `unity-dev-httpd-alb-443241596.us-west-2.elb.amazonaws.com`
    3. Log into Shared Services account
    4. Go to EC2
    5. Log into (e.g. using SSM connect) `shared-services-httpd` instance.
    6. `sudo su - ubuntu`
    7. `cd /etc/apache2/sites-enabled`
    8. `sudo vi unity-cs.conf`
    9. TODO: Link in future script that creates/modifies HTTPD shared services configuration.
    10. Edit the file to conform to the [instructions here](https://unity-sds.gitbook.io/docs/developer-docs/common-services/docs/developers-guide/httpd-server-deployment/shared-services-httpd-site-configurations), to add a block similar to:

    ```
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/emit/dev/"
    RewriteRule /emit/dev/(.*) ws://emit-dev-httpd-alb-875152633.us-west-2.elb.amazonaws.com:8080/$1 [P,L] [END]

    <Location "/emit/dev/">
        ProxyPreserveHost on
        AuthType openid-connect
        Require valid-user
        
        ProxyPass "http://emit-dev-httpd-alb-875152633.us-west-2.elb.amazonaws.com:8080/emit/dev/"
        ProxyPassReverse "http://emit-dev-httpd-alb-875152633.us-west-2.elb.amazonaws.com:8080/emit/dev/"
    </Location>
    ```


5. Restart HTTPD:\
   `sudo systemctl restart apache2`
