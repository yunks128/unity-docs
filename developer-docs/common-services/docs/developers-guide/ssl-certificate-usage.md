# SSL Certificate Usage

The most common reason for needing an SSL certificate is to create a secure listener for a load balancer target group. Some other usages include adding a certificate to a cloudfront endpoint or an API Gateway.

{% hint style="info" %}
Note, the certificates created for each venue assume that the user will be retrieving the resource through the api gateway or the HTTPD proxy. The certificate is given the \*.mdps.mcp.nasa.gov" naming convention, and will throw errors if access directly (e.g. `https://my-random-alb-52301ddb8d-563022104.us-west-2.elb.amazonaws.com)`
{% endhint %}

### Adding a secure listener to a Load Balancer

The ARN of the SSL certificate you should be using can be found in the SSM paramters for deploytime configuration.

{% hint style="info" %}
The ARN of the SSL certificate you should be using can be found in the SSM parameters for deploy-time configuration. The parameter is named **/unity/account/network/ssl**
{% endhint %}

#### Management Console

1. To Interactively create an SSL listener for an existing load balancer, navigate to the load balancer you wish to create the listener for.
2. under "listeners and rules", click `add listener`
3. Under protocol, select `https` and port should be 4443 if available. If another listener is using port 4443, select another available port.

{% hint style="danger" %}
Whatever port you choose, you **must** ensure that the security group on the load balancer allows outside connections on that port.&#x20;
{% endhint %}

1. Select forward to target group (default)
2. Select a target group to which you want this listerner for forard to. The target group does not need to be over a secure channel (e.g. port 443). SSL termination can end at the load balancer.
3. Under Secure Listener Settings, all defaults are fine- under Certificate (from aCM) select the pre-populated certificate (shows the domain and certificate id). There is probably only one.
4. Click 'add' and your listener should be set up.

The SSL policy, by default, is `ELBSecurityPolicy-TLS13-1-2-2021-06`.&#x20;

#### Terraform

In terraform, follow "Forward Action" the configuration for [https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb\_listener](https://registry.terraform.io/providers/hashicorp/aws/latest/docs/resources/lb\_listener)&#x20;

Here, the certificate\_arn should be the value retrieved from the parameter store. The SSL policy, by default if `ELBSecurityPolicy-2016-08` when created through terraform.

{% hint style="danger" %}
Whatever port you choose, you **must** ensure that the security group on the load balancer allows outside connections on that port.&#x20;
{% endhint %}

### Adding a certificate to a Cloud Front distribution endpoint

Coming soon
