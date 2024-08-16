# HTTPD Site Configurations

This page shows the common configurations and different configurations of each Unity shared service environment.

## Unity Dev HTTPD Site Configuration Example



{% code lineNumbers="true" fullWidth="true" %}
```apacheconf
<VirtualHost *:443>
    Define UNITY_SERVER_NAME unity-shared-ervices-httpd-server
    Define UNITY_OIDC_CLIENT_ID <client_id_of_cognito_client>
    Define UNITY_COGNITO_USER_POOL_ID <cognito_user_pool_id>
    Define UNITY_OIDC_CLIENT_SECRET <client_secret_of_cognito_client>
    Define UNITY_OIDC_REDIRECT_URI https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    Define UNITY_OIDC_OIDC_CRYPTO_PASSPHRASE https://www.dev.mdps.mcp.nasa.gov:4443/unity/dev/redirect-url
    
    ServerName ${UNITY_SERVER_NAME}
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
 
    RewriteEngine On
 
    RewriteCond %{HTTP:Connection} Upgrade [NC]
    RewriteCond %{HTTP:Upgrade} websocket [NC]
    RewriteCond %{REQUEST_URI} "/unity/dev/"
    #RewriteRule /unity/dev/(.*) ws://unity-dev-httpd-alb-217434782.us-west-2.elb.amazonaws.com:8080/$1 [P,L] [END]
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
    </Location>
 
 
</VirtualHost>
```
{% endcode %}

&#x20;
