---
description: >-
  MMO Admins must request and validate certificate requests for usage in an mdps
  venue.
---

# Venue Certificate Creation

To secure communication to services, we rely on HTTPS connections utilizing SSL certificates. The following process identifies how an **MMO Admin** can request and validate the request for a mdps wildcard certificate.

The instructions provided are for the interactive user ssl creation via the AWS console. These steps can be mostly automated in the future.&#x20;

### In the **project** **venue** account

1. Log in to the AWS console
2. Navigate to the AWS Certificate Manager section. There are probably _no_ certificates avilabale when running through this process.
3. From the left hand navigation pane, select "Request certificate"
4. Request a public certificate
5. For the "fully qualified domain name" enter the environment specific wildcard certificate:
   1. Production: `*.mdps.mcp.nasa.gov`
   2. Test: `*.test.mdps.mcp.nasa.gov`
   3. dev: `*.dev.mdps.mcp.nasa.gov`
6. Select DNS validation, and leave the algorithm at the default RSA 2048 value.
7. Submit the request
8. The status of the certificate should be _pending_ and it's looking for some DNS entries to be created to validate that the certificate request is being made by someone who has access to, or who can request modifications to, the domain for which the certificate is valid. This makes it so we cannot request a certificate for "google.com" and have Amazon provide it to us.
9. Download the Domain information to a CSV file for use in future steps.

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-07-01 at 12.06.21 PM (2).png" alt=""><figcaption><p>Certificate View information after Request</p></figcaption></figure>

### In the Shared Service account&#x20;

Run the following steps in the shared service accounts (e.g. unity-dev, unity-test, unity-prod)

1. Login to the AWS account for the shared service environment.&#x20;
2. Navigate to Route53 component page
3. Select "Hosted Zones" and click on the hosted zone for which the request certificate applies. For example, if i requested a certificate for "\*.test.mdps.mcp.nasa.gov", then i would select the "test.mdps.mcp.nasa.gov" hosted zone.
4. In the 'Records' area, click "create entry".&#x20;
5. Select "simple routing" as the type of entry to create.&#x20;
6. Select "define simple record'
7. For the Record name, copy the values from the above downlaoded CSV. the name in the CSV will include the `mdps.mcp.nasa.gov` portions, we only need the first value.
8. For the Record type, select `CNAME`
9. for the value, add in the `CNAME Value` from the downloaded CSV.
10. Click define simple record
11. click "create records" to create the entry in our route53 hosted zone.

{% hint style="info" %}
The CNAME value entries end with a period. This must be included.
{% endhint %}

### Back in the project venue account

1. Navigate to ACM
2. List the certificates
3. Select the certificate you requested above - there is probably only one.
4. Once the CNAME has been create in the shared services account, it shouldn't take more than several (3-5) minutes for the certificate to have the "issued" status.

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-07-01 at 12.20.22 PM.png" alt=""><figcaption><p>Certificate after successful DNS validation</p></figcaption></figure>

5. Click on the certificate ID for more details. Copy the ARN of the certificate&#x20;
6. Create an SSM Paramter for the SSL Certificate. This is good for the entire account, even if there are multiple venue deployments in the account.
   1. Navigate to the SSM parameter store (System Manager > Parameter Store)
   2. Create a paramter
   3. The name of the parameter should be `/unity/account/network/ssl` and the value should be a text value of the SSL Arn found in step 5.

<figure><img src="../../../../.gitbook/assets/Screenshot 2024-07-01 at 12.27.54 PM.png" alt=""><figcaption><p>SSM Parameter entry for SSL certificate. Red to obfuscate account numbers and unique identerfiers.</p></figcaption></figure>

### Next Steps

With the valid certificate, teams can now utilize this certificate in their load balancers, cloud formation, and other entries to enforce SSL.

Documentation is up to date as of 7/1/2024
