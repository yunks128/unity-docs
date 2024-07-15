---
description: Instructions to deploy HTTPD server on EC2
---

# HTTPD Server Deployment

An HTTPD server deployed on an EC2 instance is used as a proxy to route network requests to relevant blackened services such as Management Console, JupyterHub and other HTTPD servers. The EC2 based deployment was selected to enable rapid experimenting and troubleshooting compared to the ECS version of HTTPD. Once we have a solid set of configurations, the venue level HTTPD server will be migrated from EC2 to ECS.

## The steps to deploy HTTPD on EC2

1. Create a new security group (E.g.: httpd-sec-group) with only traffic allowed from port 22.
2. Launch an Ubuntu based EC2 instance with,

&#x20;      \- The AMI "MCP Ubuntu 20.04 CSET 20240515T143543"

&#x20;      \- Size of the EC2 instance can be Micro (or larger if it is required to handle many requests)

3. Connect to the EC2 instance with Session Manager.
4. Install Apache 2 (The new Ubuntu version of HTTPD) on Ubuntu as follows:

```
sudo apt update
sudo apt install apache2
```

3. Enable Apache2 modules with the following commands:

```
sudo a2enmod  http
sudo a2enmod  headers
sudo a2enmod  proxy
sudo a2enmod  proxy_html
sudo a2enmod  proxy_http
sudo a2enmod  proxy_wstunnel
sudo a2enmod  ssl
sudo a2enmod  rewrite
```

6. Generate self-signed SSL certificates with the following command:

```
sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/apache-selfsigned.key -out /etc/ssl/certs/apache-selfsigned.crt
```

Provide appropriate values as shown in the following example to generate the certificates (feel free to change the values such as email address and common name as required).

```
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:CA
Locality Name (eg, city) []:LA
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Unity
Organizational Unit Name (eg, section) []:CS
Common Name (e.g. server FQDN or YOUR name) []:shared-services-httpd-unity-test
Email Address []:test@test.com
```



7. Restart Apache2 (httpd) &#x20;

```
sudo systemctl restart apache2
```

&#x20;

## Steps to setup a load balancer to httpd server

Detailed instructions on creating an Application Load Balancer available at: [https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-application-load-balancer.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-application-load-balancer.html). The following instructions provides a summary of steps.

1. Create a load balancer target group (E.g.: ucs-httpd-server-tg) which the ability to access instances on HTTPS port 443 ([https://us-west-2.console.aws.amazon.com/ec2/home?region=us-west-2#TargetGroups:](https://us-west-2.console.aws.amazon.com/ec2/home?region=us-west-2#TargetGroups:)).
   * Add the EC2 instance created above to the target group.
   * Setup health check for HTTPS and path /
2. Create a security group for an Application Load Balancer (E.g.: ucs-httpd-alb-sec-group).

&#x20;        \- Allow traffic to TCP port 443 from required sources.

2. Create an Application Load Balancer and (E.g.: ucs-httpd-alb) and use the security group created above.
   * Associate the target group created above to the load balancer.
   * Request a certificate (from ACM)
   * Note the DNS URL of the Application Load balancer.
3. Update the security group of EC2 instance created above (E.g.: httpd-sec-group)  to allow traffic to port 443 only from Application Load Balancer security group (E.g.: ucs-httpd-alb-sec-group) created above.&#x20;
4. Access the DNS URL of the Application Load balancer and see if it shows the default page of Apache 2 (httpd) server.&#x20;

## Steps to create a new site and setup

1. Connect to the EC2 instance hosting httpd with Session Manager.
2. Change directory to `/etc/apache2/`

&#x20;           `cd /etc/apache2/`

3. List the sites enabled as follows:

&#x20;             `ls sites-enabled/`

4. The above command will show the list of sites enabled by default (E.g.: `000-default.conf`).
5. Disable the sites enabled by default with the following command.

&#x20;               `sudo a2dissite <site name>`

&#x20;         E.g.:&#x20;

&#x20;                `sudo a2dissite 000-default.conf`

6. Create a new site file at sites-available/ directory.

&#x20;                  `vi sites-available/unity-cs.conf`

7. Add the following content to the sites-available/unity-cs.conf file.

```
<VirtualHost *:443>
    ServerName unity.httpd.server
    ServerAlias unity.httpd.server
    ServerAdmin unity-cs@test.com

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off
</VirtualHost>
```



8. Enable the newly created site as follows.

&#x20;                  `sudo a2ensite unity-cs.conf`



## How to proxy a website through httpd server?

The official documentation available at [https://httpd.apache.org/docs/2.4/en/howto/reverse\_proxy.html](https://httpd.apache.org/docs/2.4/en/howto/reverse\_proxy.html) shows how to setup a reverse proxy with httpd server .

There are many configurations related with this topic. However, if it required to setup a basic proxy, the following syntax can be used.

```
ProxyPass /example http://www.example.com/
ProxyPassReverse /example http://www.example.com/

```

A detailed example is provided in the following section.

If the application/website hidden behind the proxy does not have sone of the  paths defined as absolute paths, then it is required to rewrite paths using the rewrite module as follows.

[https://httpd.apache.org/docs/2.4/rewrite/intro.html](https://httpd.apache.org/docs/2.4/rewrite/intro.html)

## &#x20;How to install and enable mod\_auth\_openidc?

To use Cognito authentication with Apache httpd server, it is required to use the module called mod\_auth\_openidc. \
\
You can install and enable mod\_auth\_openidc as follows.

```
sudo apt-get install libapache2-mod-auth-openidc
```

```
sudo a2enmod auth_openidc
```

The following httpd site configuration has an example showing how to use mod\_auth\_openidc with Cognito.\


<pre><code>&#x3C;VirtualHost *:443>
<strong>    ServerName httpd-experimeantal-alb-************.us-west-2.elb.amazonaws.com
</strong>    ServerAdmin postmaster@unity.httpd
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off

    RewriteEngine On
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteRule /unity/dev/(.*) wss://ucs-httpd-alb-********.us-west-2.elb.amazonaws.com:4443/$1 [P,L] [END]

    ProxyRequests Off
    
    OIDCScope "openid email profile"
    OIDCProviderMetadataURL https://cognito-idp.us-west-2.amazonaws.com/&#x3C;COGNITO_USER_POOL_ID>/.well-known/openid-configuration
    OIDCClientID &#x3C;COGNITO_CLIENT_ID>
    OIDCClientSecret &#x3C;COGNITO_CLIENT_SECRET>

    # OIDCRedirectURI is a vanity URL that must point to a path protected by this module but must NOT point to any content
    OIDCRedirectURI https://httpd-experimeantal-alb-************.us-west-2.elb.amazonaws.com:4443/redirect-url
    OIDCCryptoPassphrase *******************


   &#x3C;Location / >
      ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
    &#x3C;/Location>

    &#x3C;Location /path1 >
      ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user

       ProxyPass https://www.site1.com
       ProxyPassReverse https://www.site1.com
    &#x3C;/Location>

    &#x3C;Location /path2 >
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user

       ProxyPass https://www.site2.com
       ProxyPassReverse https://www.site2.com
    &#x3C;/Location>

    &#x3C;Location /unity/dev>
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user

       # Added to point to httpd within the unity-venue-dev account
       ProxyPass  https://ucs-httpd-alb-*********.us-west-2.elb.amazonaws.com:4443
       ProxyPassReverse  https://ucs-httpd-alb-**********.us-west-2.elb.amazonaws.com:4443
    &#x3C;/Location>     
 &#x3C;/VirtualHost>                        
</code></pre>



&#x20;\
More deatils on mod\_auth\_openidc can be found on [https://github.com/OpenIDC/mod\_auth\_openidc](https://github.com/OpenIDC/mod\_auth\_openidc)
