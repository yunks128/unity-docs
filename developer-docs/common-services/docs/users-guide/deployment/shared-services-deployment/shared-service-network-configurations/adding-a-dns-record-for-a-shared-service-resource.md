# Adding a DNS record for a shared service resource

The shared service (dev, test, production) should have only a few services that are required. Most shared service API and UI based components should be accessible through the www.\* (UI) and api.\* subdomains, and the inclusion of a new _endpoint_ shouldn't not be done trivially.

In the following example,w e'll show how to add the 'www' dns entries. These point at some service that responds with web content (e.g. HTML pages).

## Prerequisites&#x20;

Some service is needed, either an EC2 instance, Load Balancer, Cloudfront distribution or other component that will be mapped to our DNS name. This service should be HTTPS _capable_ - that is, securely encrypt traffic to the endpoint.&#x20;

In the above www case, we will be utilizing an AWS application load balancer.

## Enabling HTTPS traffic

To enable secure DNS, the load balancer above needs to securely handle the traffic. This means applying an ACM managed certificate for the secure listener. This is done when adding a listener. The default port (443) may already be in use, or may not be reachable from the public internet, so you'll need to make sure whatever port used to communicate with the load balancer .reachable destination&#x20;

<figure><img src="../../../../../../../.gitbook/assets/Screenshot 2024-04-29 at 2.07.42 PM.png" alt=""><figcaption><p>Adding a HTTPS based listener</p></figcaption></figure>

The certificate used during this process should be one that matches the domain name of the DNS you're enabling. So if you want to create www.example.com, then the certificate needs to be good for `*.example.com` or `www.example.com`.&#x20;

{% hint style="danger" %}
After setup, if you access the load balancer directly (e.g. not using the DNS name) then you will recieve the "Your connection is not secure" mesage from the browser as the certificate (\*.example.com) does not match the domain name being accessed!
{% endhint %}

## Enable DNS

Once you have your listener set up, it's time to create the DNS entries so that a "human" url can be used. Log in to route53, and look at your hosted zone- this must be a PUBLIC hsoted zone for this to be a real, internet reachable URL. private hosted zones can be "anything" you want, as they are not resolvable unless within your VPC.

<figure><img src="../../../../../../../.gitbook/assets/Screenshot 2024-04-29 at 2.12.56 PM.png" alt=""><figcaption></figcaption></figure>

From the set of DNS records for your hosted zone, click "create record."

**Simple Routing** works for our use cases, so select that. Then you'll need to use **define simple record.**

Here is where you'll create the subdomain (human readable name) and connect it to your service- in this example the load balancer.

<figure><img src="../../../../../../../.gitbook/assets/Screenshot 2024-04-29 at 1.23.34 PM.png" alt=""><figcaption><p>An example record for the www subdomain</p></figcaption></figure>

Here we take a subdomain (www) and set it up to point at the load balancer previously created. We define **BOTH** an A and AAAA record, for IPV4 and IPV6 addressing.&#x20;

<figure><img src="../../../../../../../.gitbook/assets/Screenshot 2024-04-29 at 1.23.40 PM.png" alt=""><figcaption></figcaption></figure>

Once both simple records are defined, we "**create records**" and they will be added to the DNS. The DNS may take a moment to update, but then you should have access from your created name (www...) to your back end AWS service.

{% hint style="danger" %}
This is simple a **NAME** for an existing service. the DNS entry does not offer any security to the underlying application. You must still use security rules to ensure only the correct ports are accessible from the public internet.
{% endhint %}
