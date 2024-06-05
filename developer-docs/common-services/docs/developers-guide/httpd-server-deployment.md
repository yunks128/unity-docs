---
description: Instructions to deploy HTTPD server on EC2
---

# HTTPD Server Deployment

An HTTPD server deployed on an EC2 instance is used as a proxy to route network requests to relevant blackened services such as Management Console, JupyterHub and other HTTPD servers. The EC2 based deployment was selected to enable rapid experimenting and troubleshooting compared to the ECS version of HTTPD. One we have a solid set of configurations, the venue level HTTPD server will be migrated from EC2 to ECS.

## The steps to deploy HTTPD on EC2

1. Create a new security group (E.g.: httpd-sec-group) with only traffic allowed from port 22.
2. Launch an Ubuntu based EC2 instance with,

&#x20;      \- The AMI MCP Ubuntu 20.04 CSET 20240515T143543

&#x20;      \- Size of the EC2 instance can be Micro (or larger if it is required to handle many requests)

3. Connect to the EC2 instance with Session Manager.
4. Install Apache 2 (The new Ubuntu version of HTTPD) on Ubuntu as follows.

```
sudo apt update
sudo apt install apache2
```

3. Enable Apache2 modules with the following commands.

```
sudo a2enmod  http
sudo a2enmod  headers
sudo a2enmod  proxy
sudo a2enmod  proxy_html
sudo sudo a2enmod  proxy_http
sudo a2enmod  proxy_wstunnel
sudo a2enmod  ssl
sudo a2enmod  rewrite
```



6. Restart Apache2 (httpd)

&#x20;        `systemctl restart apache2`

&#x20;

## Steps to setup a load balancer to httpd server

Detailed instructions on creating an Application Load Balancer available at: [https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-application-load-balancer.html](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/create-application-load-balancer.html). The following instructions provides a summary of steps.

1. Create a security group for an Application Load Balancer (E.g.: ucs-httpd-alb-sec-group).

&#x20;        \- Allow traffic to TCP port 8080 and 4443 from required sources.

2. Create an Application Load Balancer and (E.g.: ucs-httpd-alb) and use the security group created above.
3. Note the DNS URL of the Application Load balancer.
4. Create a load balancer target group (E.g.: ucs-httpd-server-tg).

* Add the EC2 instance created above to the target group.
* Modify the security group.
* Setup health check for HTTP and path /

5. Associate the target group created above to the load balancer.
6. Update the security group of EC2 instance created above (E.g.: httpd-sec-group)  to allow traffic to port 80 only from Application Load Balancer security group (E.g.: ucs-httpd-alb-sec-group) created above.&#x20;
7. Access the DNS URL of the Application Load balancer and see if it shows the default page of Apache 2 (httpd) server.&#x20;

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
<VirtualHost *:80>
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



If the application/website hidden behind the proxy does not have sone of the  paths defined as absolute paths, then it is required to rewrite paths using the rewrite module as follows.

[https://httpd.apache.org/docs/2.4/rewrite/intro.html](https://httpd.apache.org/docs/2.4/rewrite/intro.html)

&#x20;

&#x20;

&#x20;