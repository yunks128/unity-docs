# Token Management



This topic is also discussed in detailed in the following contents:

* [Unity Security Authentication and Authorization - Party 2 Slide Deck](https://docs.google.com/presentation/d/1wNppS59cjFRivjkim6OdFNoTO-n0E2hGG66lvBw\_EC8/edit#slide=id.g149160e0eee\_2\_42)
* [Unity Security Authentication and Authorization - Party 2 Video Recording](https://jpl.webex.com/webappng/sites/jpl/recording/5b172d410abd103b9afa005056818699/playback)

### Unity uses OAuth2 and OpenID Connect (OIDC)

#### OAuth 2.0 is an industry-standard protocol for authorization

* An open standard for authorization
* https://oauth.net/2/

#### OpenID Connect is built on the OAuth 2.0 protocol and uses an additional JSON Web Token (JWT), called an ID token

* An open standard for decentralized authentication
* Supports Federated Identity (login with Google, Facebook, Amazon, Apple, any compatible OIDC provider such as JPL SSO login) https://openid.net/connect/

In Unity, we are using above industry standards followed by many applications/organizations.

A list of OAuth providers available at https://en.wikipedia.org/wiki/List\_of\_OAuth\_providers

### Token Types

| Token Type    | Purpose                                                                                                                                                                                                                                                                   | Expiry                                     |
| ------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------ |
| Access Token  | Meant to be read and validated by the API. Can be used to gain access to resources by using them as bearer tokens. Should never be decoded by the client.                                                                                                                 | Short-lived(Default 60 minutes in Cognito) |
| ID Token      | Contains information about what happened when a user authenticatedIntended to be read by the OAuth client. May contain information about the user such as their name or email address, although that is not a requirement of an ID token. Should never be sent to an API. | Short-lived(Default 60 minutes in Cognito) |
| Refresh Token | Used to obtain a renewed access token.                                                                                                                                                                                                                                    | Long-lived (Default 30 days in Cognito)    |

### Obtaining Cognito Tokens in Jupyter Notebooks

Approaches to obtain Cognito tokens are documented in https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line

[Approach 1: Using Curl Command](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-1-using-curl-command)

[Approach 2: Using AWS CLI](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-2-using-aws-cli)

[Approach 3: Using Python Requests (in Jupyter Notebooks etc.)](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-3-using-python-requests-in-jupyter-notebooks-etc)

[Approach 4: Using AWS SDK for Python - Boto3 (in Jupyter Notebooks etc.)](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-4-using-aws-sdk-for-python---boto3-in-jupyter-notebooks-etc)

#### Limitations

The approaches mentioned in the previous slides can only obtain a Cognito token for a user created in a Cognito user pool (not for users in federated identify providers such as Google, Facebook, JPL SSO)

#### Possible Workaround for Future Use Cases (to be researched and tested):

Try to reuse the tokens received by JupyterHub during the initial login.

* Implement a custom authenticator based on GenericOAuthenticator
* Read the tokens inside the custom authenticator
* Have the spawner set an environment variables in the pre\_spawn\_start method to pass tokens to the JupyterLab
* Read the tokens by accessing environment variables in Jupyter Notebook

Related GitHub issue: [Get access token from Jupyter notebook console after authenticating with Keycloak](https://github.com/jupyterhub/oauthenticator/issues/203)

### How to Handle Token Expiry?

Implement a python function called get\_tokens() as follows.

```python
IF there is an unexpired token, THEN
    Return tokens

ELSE
    IF there are NO tokens, THEN
        Get new tokens with user credentials
        When tokens are received, decode the access token and store (cache) the expiry date (epoch value) in a variable (expiry-date)

    IF there is an expired token (detected by checking the expiry-date variable), THEN
       IF the refresh token is unexpired, THEN
           Get a new access token and ID token using the refresh token
        ELSE
           Get new tokens with user credentials
```
