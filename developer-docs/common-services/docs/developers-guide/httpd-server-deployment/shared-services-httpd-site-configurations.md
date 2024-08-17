# Shared Services HTTPD Site Configurations

The Shared Services HTTPD server acts as a portal for venue level HTTPD servers and other shared services. There are multiple shared service HTTPD servers for Dev, Test and Production. Some of the configurations follow the same pattern among different environments.

## Unity HTTPD Site Configuration Template

The following section shows a common configurations template that can be used for any shared services HTTPD server.&#x20;

This template has,

1. A section with variable definitions that can be completed with values specific to each envrionment
2. The actual cofniguation with above variable substitutions
3. A section with a pseudo code to describe the nature of common location blocks and rewrite rules

<pre class="language-apacheconf"><code class="lang-apacheconf">&#x3C;VirtualHost *:443>

    ##################################################################
    # Define the following variables with environment specific values 
    ##################################################################
    
    # The prefered name of the Unity HTTPD server
    Define UNITY_HTTPD_SERVER_NAME unity-shared-services-httpd-server
    
    # The values for following Cognito related variables can be obtained from the Unity CS team
    # OR checking the Shared Services Cognito pool in a specific envrionment (if you have access)
    Define UNITY_OIDC_CLIENT_ID &#x3C;client_id_of_cognito_client>
    Define UNITY_COGNITO_USER_POOL_ID &#x3C;cognito_user_pool_id>
    Define UNITY_OIDC_CLIENT_SECRET &#x3C;client_secret_of_cognito_client>
    
    
    # For the scope of this server, the following passphrase can be any random string.
    # For more information: https://github.com/OpenIDC/mod_auth_openidc/blob/master/auth_openidc.conf#L16 
    Define UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE &#x3C;any_random_passphrase>
    
    # The is a vanity URL that must point to a path protected by this module but must NOT point to any content
    # The Cognito app client which has the above UNITY_OIDC_CLIENT_ID should have this URL as an "Allowed callback URL"
    Define UNITY_OIDC_REDIRECT_URI https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    
    
    ##################################################################
    # Apache HTTPD Configurations with above variable substitutions
    ##################################################################
    
    ServerName ${UNITY_HTTPD_SERVER_NAME}
    ServerAlias ${UNITY_SERVER_NAME}
    ServerAdmin postmaster@unity.httpd
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    
    # The following SSL certificate paths are based on the HTTPD server deployment
    # explained in https://app.gitbook.com/o/xZRqGQeQXJ0RP4VMj7Lq/s/UMIRhLdbRQTvMWop8Il9/~/changes/592/developer-docs/common-services/docs/developers-guide/httpd-server-deployment
    SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off
    ProxyRequests Off
 
    OIDCScope "openid email profile"
    OIDCProviderMetadataURL https://cognito-idp.us-west-2.amazonaws.com/${UNITY_COGNITO_USER_POOL_ID}/.well-known/openid-configuration
    OIDCClientID ${UNITY_OIDC_Client_ID}
    OIDCClientSecret ${UNITY_OIDC_CLIENT_SECRET}
 
    # OIDCRedirectURI is a vanity URL that must point to a path protected by this module but must NOT point to any content
    OIDCRedirectURI ${UNITY_OIDC_REDIRECT_URI}
    OIDCCryptoPassphrase ${UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE}
 
 
    #############################################################################
    # Location blocks and optional rewrite rules for venues and shared services
    #############################################################################
    
     # The following pseudo code shows how rewrite rules and Location blocks can be 
     # repeated based on a number of paths and URLs available. At the moment this section 
     # should be added manually. The next section of this page shows more concrete examples
     # of this configuration block in Dev, Test and Production environments. 
     FOR EACH ${PATH},${URL} in (('/unity/dev',ALB-URL), ('/galen/dev1',ALB-URL2), ...):
<strong>     {
</strong>            # Rewrite rules are optional and the following code block is a common
            # rewrite rules block
            RewriteEngine On
            RewriteCond %{HTTP:Connection} Upgrade [NC]
            RewriteCond %{HTTP:Upgrade} websocket [NC]
            RewriteCond %{REQUEST_URI} "${PATH}"
            RewriteRule ${PATH}(.*) ws://${URL}${PATH}$1 [P,L] [END]
        
            # The location blocks are required to integrate paths with backend URLs
            &#x3C;Location "${PATH}">
               ProxyPreserveHost on
               AuthType openid-connect
               Require valid-user
        
               # Added to point to httpd within the unity-venue-dev account
               ProxyPass "${URL}${PATH}"
               ProxyPassReverse "${URL}${PATH}"
            &#x3C;/Location>
        
     }
 
&#x3C;/VirtualHost>
</code></pre>

## Unity Dev HTTPD Site Configuration Example

```apacheconf
<VirtualHost *:443>

    ##################################################################
    # Define the following variables with environment specific values 
    ##################################################################
    
    # The prefered name of the Unity HTTPD server
    Define UNITY_HTTPD_SERVER_NAME unity-shared-services-httpd-server
    
    # The values for following Cognito related variables can be obtained from the Unity CS team
    # OR checking the Shared Services Cognito pool in a specific envrionment (if you have access)
    Define UNITY_OIDC_CLIENT_ID <client_id_of_cognito_client>
    Define UNITY_COGNITO_USER_POOL_ID <cognito_user_pool_id>
    Define UNITY_OIDC_CLIENT_SECRET <client_secret_of_cognito_client>
    Define UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE <any_random_passphrase>
    
    # The is a vanity URL that must point to a path protected by this module but must NOT point to any content
    # The Cognito app client which has the above UNITY_OIDC_CLIENT_ID should have this URL as an "Allowed callback URL"
    Define UNITY_OIDC_REDIRECT_URI https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    
    
    ##################################################################
    # Apache HTTPD Configurations with above variable substitutions
    ##################################################################
    
    ServerName ${UNITY_HTTPD_SERVER_NAME}
    ServerAlias ${UNITY_SERVER_NAME}
    ServerAdmin postmaster@unity.httpd
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off
    ProxyRequests Off
 
    OIDCScope "openid email profile"
    OIDCProviderMetadataURL https://cognito-idp.us-west-2.amazonaws.com/${UNITY_COGNITO_USER_POOL_ID}/.well-known/openid-configuration
    OIDCClientID ${UNITY_OIDC_Client_ID}
    OIDCClientSecret ${UNITY_OIDC_CLIENT_SECRET}
 
    # OIDCRedirectURI is a vanity URL that must point to a path protected by this module but must NOT point to any content
    OIDCRedirectURI ${UNITY_OIDC_REDIRECT_URI}
    OIDCCryptoPassphrase ${UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE}
 
 
    #############################################################################
    # Location blocks and optional rewrite rules for venues and shared services
    #############################################################################
    
    RewriteEngine On
 
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/unity/dev/"
    RewriteRule /unity/dev/(.*) ws://unity-dev-httpd-alb-*********.us-west-2.elb.amazonaws.com:8080/unity/dev/$1 [P,L] [END]
 
    <Location "/unity/dev/">
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
 
       # Added to point to httpd within the unity-venue-dev account
       ProxyPass http://unity-dev-httpd-alb-*********.us-west-2.elb.amazonaws.com:8080/unity/dev/
       ProxyPassReverse http:///unity-dev-httpd-alb-*********.us-west-2.elb.amazonaws.com:8080/unity/dev/
       RequestHeader     set "X-Forwarded-Proto" expr=%{REQUEST_SCHEME}
    </Location>
 
    ProxyPass "/data/" http://*.*.*.*:8005/data/
    ProxyPassReverse "/data/" http://*.*.*.*:8005/data/
 
    <Location /data>
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
       
       ProxyPass http://*.*.*.*:8005/data/
       ProxyPassReverse http://*.*.*.*:8005/data/
    </Location>
 
 
</VirtualHost>
```



## Unity Test HTTPD Site Configuration Example

<pre class="language-apacheconf"><code class="lang-apacheconf"><strong>&#x3C;VirtualHost *:443>
</strong>   
    ##################################################################
    # Define the following variables with environment specific values 
    ##################################################################
    
    # The prefered name of the Unity HTTPD server
    Define UNITY_HTTPD_SERVER_NAME unity-shared-services-httpd-server
    
    # The values for following Cognito related variables can be obtained from the Unity CS team
    # OR checking the Shared Services Cognito pool in a specific envrionment (if you have access)
    Define UNITY_OIDC_CLIENT_ID &#x3C;client_id_of_cognito_client>
    Define UNITY_COGNITO_USER_POOL_ID &#x3C;cognito_user_pool_id>
    Define UNITY_OIDC_CLIENT_SECRET &#x3C;client_secret_of_cognito_client>
    Define UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE &#x3C;any_random_passphrase>
    
    # The is a vanity URL that must point to a path protected by this module but must NOT point to any content
    # The Cognito app client which has the above UNITY_OIDC_CLIENT_ID should have this URL as an "Allowed callback URL"
    Define UNITY_OIDC_REDIRECT_URI https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    
    
    ##################################################################
    # Apache HTTPD Configurations with above variable substitutions
    ##################################################################
    
    ServerName ${UNITY_HTTPD_SERVER_NAME}
    ServerAlias ${UNITY_SERVER_NAME}
    ServerAdmin postmaster@unity.httpd
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off
    ProxyRequests Off
 
    OIDCScope "openid email profile"
    OIDCProviderMetadataURL https://cognito-idp.us-west-2.amazonaws.com/${UNITY_COGNITO_USER_POOL_ID}/.well-known/openid-configuration
    OIDCClientID ${UNITY_OIDC_Client_ID}
    OIDCClientSecret ${UNITY_OIDC_CLIENT_SECRET}
 
    # OIDCRedirectURI is a vanity URL that must point to a path protected by this module but must NOT point to any content
    OIDCRedirectURI ${UNITY_OIDC_REDIRECT_URI}
    OIDCCryptoPassphrase ${UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE}
 
 
    #############################################################################
    # Location blocks and optional rewrite rules for venues and shared services
    #############################################################################
 
    RewriteEngine On
 
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/unity/test/"
    RewriteRule /unity/test/(.*) ws://unity-test-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/$1 [P,L] [END]
 
    &#x3C;Location "/unity/test/">
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
 
       # Added to point to httpd within the unity-venue-test account
       ProxyPass  http://unity-test-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/unity/test/
       ProxyPassReverse  http://unity-test-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/unity/test/
    &#x3C;/Location>
 
    &#x3C;Location /data>
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
       
       ProxyPass http://*.*.*.*:8005/data/
       ProxyPassReverse http://*.*.*.*:8005/data/
    &#x3C;/Location>
 
&#x3C;/VirtualHost>

</code></pre>



## Unity Production HTTPD Site Configuration Example

```apacheconf
<VirtualHost *:443>
   
    ##################################################################
    # Define the following variables with environment specific values 
    ##################################################################
    
    # The prefered name of the Unity HTTPD server
    Define UNITY_HTTPD_SERVER_NAME unity-shared-services-httpd-server
    
    # The values for following Cognito related variables can be obtained from the Unity CS team
    # OR checking the Shared Services Cognito pool in a specific envrionment (if you have access)
    Define UNITY_OIDC_CLIENT_ID <client_id_of_cognito_client>
    Define UNITY_COGNITO_USER_POOL_ID <cognito_user_pool_id>
    Define UNITY_OIDC_CLIENT_SECRET <client_secret_of_cognito_client>
    Define UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE <any_random_passphrase>
    
    # The is a vanity URL that must point to a path protected by this module but must NOT point to any content
    # The Cognito app client which has the above UNITY_OIDC_CLIENT_ID should have this URL as an "Allowed callback URL"
    Define UNITY_OIDC_REDIRECT_URI https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    
    
    #################################################################
    # Apache HTTPD Configurations with above variable substitutions
    #################################################################
    
    ServerName ${UNITY_HTTPD_SERVER_NAME}
    ServerAlias ${UNITY_SERVER_NAME}
    ServerAdmin postmaster@unity.httpd
    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
    SSLProxyEngine On
    SSLCertificateFile /etc/ssl/certs/apache-selfsigned.crt
    SSLCertificateKeyFile /etc/ssl/private/apache-selfsigned.key
    SSLProxyCheckPeerCN off
    SSLProxyCheckPeerExpire on
    SSLProxyCheckPeerName off
    ProxyRequests Off
 
    OIDCScope "openid email profile"
    OIDCProviderMetadataURL https://cognito-idp.us-west-2.amazonaws.com/${UNITY_COGNITO_USER_POOL_ID}/.well-known/openid-configuration
    OIDCClientID ${UNITY_OIDC_Client_ID}
    OIDCClientSecret ${UNITY_OIDC_CLIENT_SECRET}
 
    # OIDCRedirectURI is a vanity URL that must point to a path protected by this module but must NOT point to any content
    OIDCRedirectURI ${UNITY_OIDC_REDIRECT_URI}
    OIDCCryptoPassphrase ${UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE}
 
 
    #############################################################################
    # Location blocks and optional rewrite rules for venues and shared services
    #############################################################################
 
    RewriteEngine On
 
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/unity/ops/"
    RewriteRule /unity/ops/(.*) ws://unity-ops-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/$1 [P,L] [END]
 
    <Location "/unity/ops/">
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
 
       # Added to point to httpd within the unity-venue-dev account
       ProxyPass http://unity-ops-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/unity/ops/
       ProxyPassReverse http:///unity-ops-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/unity/ops/
    </Location>
 
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/asips/int/"
    RewriteRule /asips/int/(.*) ws://asips-int-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/$1 [P,L] [END]
 
    <Location "/asips/int/">
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
 
       # Added to point to httpd within the unity-venue-dev account
       ProxyPass http://asips-int-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/asips/int/
       ProxyPassReverse http://asips-int-httpd-alb-********.us-west-2.elb.amazonaws.com:8080/asips/int/
    </Location>

 
    <Location /data>
       ProxyPreserveHost on
       AuthType openid-connect
       Require valid-user
       
       ProxyPass http://*.*.*.*:8005/data/
       ProxyPassReverse http://*.*.*.*:8005/data/
    </Location>
 
</VirtualHost>

```
