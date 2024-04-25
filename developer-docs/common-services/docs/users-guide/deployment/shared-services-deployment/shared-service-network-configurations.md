# Shared Service Network Configurations

There is a single point of entry into the MDPS system, and it is "mdps.mcp.nasa.gov".&#x20;



### Delegating responsibility to a subdomain

Follow the steps on [this aws tutorial](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/dns-routing-traffic-for-subdomains.html) to delegate responsibilities of a subdomain.

We only delegate shared service subdomains so that venue services remain inaccessible through direct connections- all connections to UIs and APIs within a venue must go through the shared service routes (that is mdps.mcp.nasa.gov/**project/venue**/processing)

Following the above tutorial, we delegate responsibility for the `test.mdps.mcp.nasa.gov` and `dev.mdps.mcps.nasa.gov` to the private hosted zones in unity-test and unity-dev, respectfully.

Within each shared service venue, there are two more subdomains- `www` and `api`. The route UI and API traffic to the appropriate endpoints within a venue. Below is a diagram of what this looks like at the shared service level:

<figure><img src="../../../../../../.gitbook/assets/Domains, URLs and Certs - Page 2 (1).png" alt=""><figcaption><p>Delegation of subdomains for shared services.</p></figcaption></figure>
