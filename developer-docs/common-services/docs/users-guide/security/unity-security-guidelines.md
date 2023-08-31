# Unity Security Guidelines

#### What is Unity?

The Unity is a service-based software platform to process and analyze science data. Unity will provide a framework to bring together existing capabilities into a single solution. Data Scientists and SDS Engineers supporting R\&A projects or Flight projects will use Unity to configure & deploy a SDS to support their project needs.

Initial users of Unity will be some Earth and Planetary R\&A and Flight Projects at JPL. Long-term we want it to be a resource for all JPL and NASA Projects.

#### Why is it important to follow the security guidelines for Unity?

The Unity will be used by a wide user pool and the Unity services will be available not only to the internal JPL network, but also, to other external networks. At the same time, the Unity services will be hosted on the cloud. These factors can make Unity vulnerable to security threats and breaches, if we do not follow very strong security guidelines.

Also, it is important to use mechanisms such as authentication and authorization to make sure that only authorized users can access Unity resources including data and services. This document provides some important security guidelines to achieve this.

#### Timeline (for the Scope of Prototype)

* Authentication Mechanism Reference Implementation - 03/01/2022
  * Amazon Cognito Setup
  * JPL LDAP + Earth Data Login support
  * Basic example with one or more U-SPS components integrated (includes app2app and human2app)
* Basic Authorization Support with Amazon Cognito Groups - 03/01/2022

#### Security Recommendations:

1. In order to provide consistent authentication and authorization mechanisms, it is recommended to use Service Oriented Architecture or Micro-services Architecture for all the applications/services of Unity (The micro-service architecture is highly preferred).
2. The UI layer of the applications should not contain any sensitive business logic or sensitive data. The business logic should be handled in a service layer and the service layer should be protected by token-based authentication.
3. The https protocol should be used in all the communications (both human-to-app communications and app-to-app communications).
4. OAuth 2.0 industry standard ([RFC6749](https://datatracker.ietf.org/doc/html/rfc6749)) should be used for the authorization (authorization is the process of verifying what specific applications, files, and data a user has access to).
5. OpenID Connect industry standard ([OpenID Connect Core 1.0](https://openid.net/specs/openid-connect-core-1\_0.html)) should be used by the Unity services for authentication (authentication is the process of verifying who someone is).
6. JSON Web Tokens (JWT) should be used for the token-based authentication. JWT are an open, industry standard ([RFC7519](https://datatracker.ietf.org/doc/html/rfc7519)) method for representing claims securely between two parties.
7. AWS Security Groups and Access Control Lists (ACL) should be used to limit access to resources/services. Only the authorized consumers should be able access resources/services.
8. The Open Web Application Security Project ([OWASP](https://owasp.org)) security guidelines should be followed, wherever possible.
9. For Single Page applications (E.g.: Angular, ReactJS), Authorization Code Flow with the PKCE (Proof Key for Code Exchange, [RFC7636](https://datatracker.ietf.org/doc/html/rfc7636)) should be used instead of the deprecated Implicit Grant.
10. If developing Javascript based Single Page Applications, store access tokens out of reach of JavaScript in the browser. You could store them in a server side session, or in secure, [HttpOnly cookies](https://owasp.org/www-community/HttpOnly).

#### Authorization and Authorization Mechanisms

Amazon Cognito on AWS will be used as a user identity service. With Amazon Cognito, the Unity users can sign-in through multiple NASA specific identity providers such as JPL LDAP, Earthdata Login, and also through social identity providers such as Google, Facebook, and Amazon. Amazon Cognito provides following OAuth 2.0 grants. But only the Authorization Code Grant and the Client Credentials Grant should be used in the Unity services. The Implicit Grant should not be used in Unity services and it is deprecated now.

| Auth 2.0 Grants Supported by Amazon Cognito | When to Use                                                                                                                                                                                                                                                                                                                                                         | References and Description of OAuth 2.0 Flows                                                                                                                                                                                                                                   |
| ------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Authorization Code Grant                    | To authorize human-to-machine requests (Human-to-App requests)                                                                                                                                                                                                                                                                                                      | <p>Please see the Authorization code grant section in the following links.<br><br>https://aws.amazon.com/blogs/mobile/understanding-amazon-cognito-user-pool-oauth-2-0-grants/<br><br>https://dzone.com/articles/the-right-flow-for-the-job-which-oauth-20-flow-sho</p>         |
| Client Credentials Grant                    | <p>To authorize machine-to-machine requests (App-to-App requests)<br><br>Note: Assuming all the Unity Services are hosted on AWS Cloud, it is sufficient to use AWS Security Groups and ACLs to control access (for the scope of the prototype). However, the Client Credentials Grant can be used to add an additional layer of security, wherever applicable.</p> | <p>Please see the Client credentials grant section in the following links.<br><br><br><br>https://aws.amazon.com/blogs/mobile/understanding-amazon-cognito-user-pool-oauth-2-0-grants/<br><br>https://dzone.com/articles/the-right-flow-for-the-job-which-oauth-20-flow-sho</p> |
| Implicit Grant                              | _**Do not use. The Implicit Grant is deprecated. For Single Page applications (E.g.: Angular, ReactJS), Authorization Code Flow with the PKCE (Proof Key for Code Exchange) should be used instead of the deprecated Implicit Grant.**_                                                                                                                             | https://pragmaticwebsecurity.com/articles/oauthoidc/from-implicit-to-pkce.html#:\~:text=Summary,we%20no%20longer%20need%20today).                                                                                                                                               |

**Authorization Code Grant**

This sections explains the Authorization Code Grant, that can be used to authorize human-to-machine requests (Human-to-App requests).

![Authorization Code Grant Image](https://raw.githubusercontent.com/unity-sds/unity-cs-security/main/images/auth-code-grant.png)

1. A client tries to access a protected URL and sends an HTTP GET request to https://AUTH\_DOMAIN/oauth2/authorize, where AUTH\_DOMAIN represents the user pool’s configured domain.
2. The end user is redirected to the login page (The login page is presented to the user, if the user is not already logged-in. Otherwise, it skips the step 3 below).
3. The end user enters credentials on the relevant login page (this can be JPL LDAP login, Earthdata Login or Google login etc.).
4. After the user credentials are verified, the user is redirected to a redirect URL. This redirect also sets a code query parameter that specifies the authorization code that was issued to the user by Amazon Cognito. The Unity Application that’s hosted at the redirect URL can then extract the authorization code from the query parameters and exchange it for user pool tokens.
5. The exchange occurs by submitting a POST request to https://AUTH\_DOMAIN/oauth2/token (the user pool tokens are never exposed to the end user/web browser. Only the Unity Application receives the user pool tokens).
6. The the user pool tokens received by the Unity Application is passed as a request header alone with the request to the Unity Service Endpoint. The Unity Service Endpoint responses with data.

**For Single Page applications (E.g.: Angular, ReactJS), Authorization Code Flow with the PKCE (Proof Key for Code Exchange) should be used instead of the deprecated Implicit Grant.**

Please read following articles on Authorization Code Flow (with PKCE).

* https://blog.postman.com/pkce-oauth-how-to/
* https://www.oauth.com/oauth2-servers/pkce/
* https://datatracker.ietf.org/doc/html/rfc7636

The Unity Service Endpoint will be protected by the Amazon API Gateway as explained in the Amazon API Gateway section below.

Please see the Authorization Code Grant section in the following link for a detailed explanation. https://aws.amazon.com/blogs/mobile/understanding-amazon-cognito-user-pool-oauth-2-0-grants/

**Client Credentials Grant**

This sections explains the Client Credentials Grant, that can be used to authorize machine-to-machine requests (App-to-App requests).

![Client Credentials Grant Image](https://raw.githubusercontent.com/unity-sds/unity-cs-security/main/images/client-credentials-grant.png)

1. A Unity App Client makes a POST request to https://AUTH\_DOMAIN/oauth2/token. In order to indicate that the app is authorized to make the request, the Authorization header for this request is set as “Basic BASE64(CLIENT\_ID:CLIENT\_SECRET)“, where BASE64(CLIENT\_ID:CLIENT\_SECRET) is the base64 representation of the app client ID and app client secret, concatenated with a colon. The client secret should be obtained from the **AWS Secrets Manager**.
2. The Amazon Cognito authorization server returns a JSON object with a valid user pool access token.
3. The Unity App Client uses that access token to make requests to an associated Unity Service Endpoint.
4. The Unity Service Endpoint validates the received token and, if everything checks out, executes the request from the app.

The Unity Service Endpoint will be protected by the Amazon API Gateway as explained in the Amazon API Gateway section below.

Please see the Client Credentials Grant section in the following link for a detailed explanation. https://aws.amazon.com/blogs/mobile/understanding-amazon-cognito-user-pool-oauth-2-0-grants/

**User Authorization**

For the scope of the Unity prototype, users will be assigned to Amazon Cognito Groups and a group level authorization will be provided. The JWT tokens will contain the list of Cognito users associated with a given user. Some sample user groups (Viewer and Admin) are shown below with an example of a decoded JWT token content issued by Amazon Cognito.

```
{
    "at_hash": "zh_ureP0p1WZFdsadadas",
    "sub": "7457c91e-aeae-490f-97ab-sjsjkask",
    "cognito:groups": [
        "us-east-2_abcde_Google",
        "Viewer",
        "Admin"
        ],
    "email_verified": true,
    "iss": "https://cognito-idp.us-east-2.amazonaws.com/us-east-2_abcdefghij",
    "cognito:username": "google_107721234567890",
    "origin_jti": "3fac2719-5d8a-4e1a-aa15-dsfdsfdssf",
    "aud": "tdpfdjdhfjfhdsjhdsj",
    "identities": [
        {
        "userId": "107721234567890",
        "providerName": "Google",
        "providerType": "Google",
        "issuer": null,
        "primary": "true",
        "dateCreated": "1642105516800"
        }
    ],
    "token_use": "id",
    "auth_time": 1643255463,
    "name": "AWS Test 333",
    "exp": 1643259063,
    "iat": 1643255463,
    "jti": "885ea2e9-d973-44ac-ac9e-abcdef",
    "email": "awstestuser333@gmail.com"
}
```

#### Amazon API Gateway

Amazon API Gateway will be used as the API Gateway for Unity. It makes it easy for developers to create, publish, maintain, monitor, and secure APIs. This API Gateway supports following APIs and all the Unity services with following API types should be protected with API Gateway.

* RESTful APIs
* HTTP APIs
* WebSocket APIs

The Amazon API Gateway will be integrated with Amazon Cognito as follows.

![Cognito with API-Gateway](https://github.com/unity-sds/unity-cs-security/blob/main/images/cognito-with-API-gateway.png)

1. A Unity App Client makes a request to get an access token from Amazon Cognito. When a Unity Ap Client is making a request, the client secret should be obtained from the **AWS Secrets Manager**.
2. The Unity App Client receives an access token.
3. The Unity App Client invoke an API with the access token.
4. The Amazon API Gateway sends the access token to Amazon Cognito to validate the token.
5. The Amazon Cognito validates the access token and sends the token validity to the Amazon API Gateway. 6a. If the access token is valid, then the Amazon API Gateway validates the OAuth2 scope with the target Unity Service Endpoint. 6b. If the access token is invalid, then the Amazon API Gateway returns a 403 (Forbidden) response to the client.
6. If the OAuth2 scope is valid, then the Unity Service Endpoint responses with results.
7. The response is passed to the Unity App Client with status 200 (success) and results (if applicable).

#### Supported Identity Providers

Currently, there are plans to support following identity providers for the Unity prototype.

* JPL LDAP
* Earth Data Login support
* Google Login (Many Unity developers already use Google Login, in order to access Unity Google Workspace)
* GitHub Login (This is not directly supported on Amazon Cognito, and currently we are evaluating a workaround).

In addition to that, the following social identity providers can be easily integrated with Amazon Cognito.

* Facebook Login
* Amazon Login
* Apple Login

#### Secure Coding Guidelines

Regardless of the authentication and authorization mechanisms, and infrastructure security, an application can be vulnerable to cyber security attacks due to un-secure coding practices. It is recommended to follow, below secure coding guidelines to ensure the application-level security.

NASA Engineering Network - Secure Coding Best Practices: https://nen.nasa.gov/web/coding/links

NASA Engineering Network – Secure Coding Tutorials: https://nen.nasa.gov/web/coding/tutorials

NASA Engineering Network – Top NASA CWEs (Common Weakness Enumeration): https://nen.nasa.gov/web/coding/nasacwes

In addition to the above guidelines, it is recommended to use static code scanning tools as IDE plugins or as a part of the build process, to identify security vulnerabilities in the source code as soon as possible.

### Basic Example with Some U-SPS Components Integrated

A basic example with few Unity SPS components integrated with authentication was developed as follows as a reference implementation.

* **HySDS UI** - A copy of the HySDS UI code was obtained and authentication was enabled with Auth 2.0 Authorization Code Grant with PKCE (Proof Key for Code Exchange)
* **Secured RESTful API** - A basic Flask application with a REST endpoint was developed to return a single value (job count) that will be consumed by the HySDS UI. The Flask application was secured with an API Gateway and to consume this value, the HySDS UI should use an access token obtained from Cognito.
* **REST API Consumer** - Another Flask application was developed as a consumer of the secured Flask application. This consumer application was developed to demonstrate the Auth 2.0 Client Credential Grant that will be used for app-to-app (machine-to-machine) authentication.

#### Screenshots of UI Integration and Sign-in

1. A user accesses the URL of HySDS UI and clicks on the login button.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/before-login.png)

2. The user is redirected to the Amazon Cognito login prompt (this UI can be improved with a banner etc.). The user enters the user name and password of the prepared login method.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/cognito-login-prompt.png)

3. The user is redirected back to the HySDS UI.

* The HySDS UI executes OAuth 2.0 Authorization Code Grant with PKCE (Proof Key for Code Exchange) and obtains 3 tokens (access token, ID token and refresh token).
* The HySDS UI will be displayed to the user, if the user is authenticated AND if the the user belongs to the Unity\_Viewer group or Unity\_Admin group (Groups are retrieved by decoding the access token).
* The email address of the user is shown in the top-right corner of the page

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/after-login.png)

4. If the user does not belong to Unity\_Viewer group or Unity\_Admin group, then the following message will be displayed.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/unaothorized-access.png)

5. Also, there is a mock endpoint (a basic Flask application) deployed to represent a single Mozart REST API endpoint. This endpoint returns the job\_count and it is protected by an API Gateway (REST Gateway) integrated with Cognito. When someone tries to access this endpoint, it shows an "Unauthorized" message as follows.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/secured-api.png)

6. The HySDS passes the previously obtained access token to above secured endpoint as Authorization header (Bearer access\_token\_string) and successfully retrieves the job count (Total : 432 value) as shown below. For the purpose of this exercise and for simplicity, only one endpoint was integrated. However this can consider as an end-to-end thin slice of the authentication process.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/value-from-secured-api.png)

7. Also, when another application wants to access above secured endpoint (app-to-app/machine-to-machine authentication), the OAuth 2.0 Client Credential Grant can be used. A basic flask application was developed as a consumer for the above mock of Mozart job count endpoint. The following screenshot shows the Swagger-UI of this consume application. And this consumer can access the secured API as follows by using the Client Credential Grant.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/prototypes/app-to-app-auth.png)

#### Links to Reference Implementations in Github

HySDS UI - authentication with Auth 2.0 Authorization Code Grant with PKCE (Proof Key for Code Exchange)

* https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/hysds\_ui\_with\_auth

Secured RESTful API - protected with API Gateway

* https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/hysds\_mozart\_mock
* https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/aws\_rest\_api\_gateway

REST API Consumer - authentication with Auth 2.0 Client Credential Grant for app-to-app (machine-to-machine) communication.

* https://github.com/unity-sds/unity-cs-security/tree/main/code\_samples/hysds\_mozart\_mock\_consumer
