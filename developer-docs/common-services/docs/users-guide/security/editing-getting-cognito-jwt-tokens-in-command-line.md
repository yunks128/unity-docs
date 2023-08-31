# Editing Getting Cognito JWT Tokens in Command Line

The following approaches can be used to get JWT tokens in command line, for a user created in a Cognito User Pool.

[Approach 1: Using Curl Command](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-1-using-curl-command)

[Approach 2: Using AWS CLI](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-2-using-aws-cli)

[Approach 3: Using Python Requests (in Jupyter Notebooks etc.)](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-3-using-python-requests-in-jupyter-notebooks-etc)

[Approach 4: Using AWS SDK for Python - Boto3 (in Jupyter Notebooks etc.)](https://github.com/unity-sds/unity-cs/wiki/Getting-Cognito-JWT-Tokens-in-Command-Line#approach-4-using-aws-sdk-for-python---boto3-in-jupyter-notebooks-etc)

### Prerequisites Related with the App Client Configured Under the Cognito User Pool

* When creating an App Client under the Cognito User Pool, make sure to select the option: `"Don’t generate a client secret"` (this cannot be changed after creating the App Client).
* Make sure that the `ALLOW_USER_PASSWORD_AUTH` option is enabled for this App Client.

### Approach 1: Using Curl Command

1.  Create a JSON file called `auth.json` as follows with,

    * The username and password of the user
    * The ClientId of the related App Client configured in Cognito

    Replace `<COGNITO_CLIENT_ID>`, `<USER_NAME>` and the `<USER_PASSWORD>` with the correct values. The relevant COGNITO\_CLIENT\_ID can be obtained from the Unity CS team.

`auth.json`

```json
{
   "AuthParameters" : {
      "USERNAME" : "<USER_NAME>",
      "PASSWORD" : "<USER_PASSWORD>"
   },
   "AuthFlow" : "USER_PASSWORD_AUTH",
   "ClientId" : "<COGNITO_CLIENT_ID>"
}
```

2. Execute the curl command as follows (make sure to use the correct AWS Region instead of the `<AWS_REGION>` below).

```bash
curl -X POST --data @auth.json \
-H 'X-Amz-Target: AWSCognitoIdentityProviderService.InitiateAuth' \
-H 'Content-Type: application/x-amz-json-1.1' \
https://cognito-idp.<AWS_REGION>.amazonaws.com/
```

### Approach 2: Using AWS CLI

1. Make sure that the AWS CLI is [installed](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html) and configured in your local environment.
2. Execute the following command (Replace `<COGNITO_CLIENT_ID>`, `<USER_NAME>`, `<USER_PASSWORD>` and `<AWS_REGION>` with correct values. The relevant COGNITO\_CLIENT\_ID can be obtained from the Unity CS team).

```bash
aws cognito-idp initiate-auth --region <AWS_REGION> --auth-flow USER_PASSWORD_AUTH --client-id <COGNITO_CLIENT_ID> --auth-parameters USERNAME=<USER_NAME>,PASSWORD=<USER_PASSWORD>
```

### Approach 3: Using Python Requests (in Jupyter Notebooks etc.)

1.  Create a JSON file called `auth.json` as follows with,

    * The username and password of the user
    * The ClientId of the related App Client configured

    Replace `<COGNITO_CLIENT_ID>`, `<USER_NAME>` and the `<USER_PASSWORD>` with the correct values. The relevant COGNITO\_CLIENT\_ID can be obtained from the Unity CS team.

`auth.json`

```json
{
   "AuthParameters" : {
      "USERNAME" : "<USER_NAME>",
      "PASSWORD" : "<USER_PASSWORD>"
   },
   "AuthFlow" : "USER_PASSWORD_AUTH",
   "ClientId" : "<COGNITO_CLIENT_ID>"
}
```

2. If using a Jupyter Notebook, upload the above `auth.json` to Jupyter Notebook.
3. Execute the following python code to get the token (make sure to use the correct AWS Region instead of the `<AWS_REGION>` below).

```python
import requests
import json

url     = 'https://cognito-idp.<AWS_REGION>.amazonaws.com'

# Read auth.json file
auth_file = open("auth.json")
payload = json.load(auth_file)

# Set headers
headers = {
    'X-Amz-Target': 'AWSCognitoIdentityProviderService.InitiateAuth',
    'Content-Type': 'application/x-amz-json-1.1'
}

# POST request
res = requests.post(url, json=payload, headers=headers)

# Print all tokens
print(res.json())

# Print access token
access_token = res.json()['AuthenticationResult']['AccessToken']
print(access_token)

```

### Approach 4: Using AWS SDK for Python - Boto3 (in Jupyter Notebooks etc.)

1. Create a JSON file called `auth_params.json` as follows with the username and password of the user. (Replace `<USER_NAME>` and the `<USER_PASSWORD>` with the correct values).

`auth_params.json`

```json
{
    "USERNAME" : "<USER_NAME>",
    "PASSWORD" : "<USER_PASSWORD>"
}
```

2. If using a Jupyter Notebook, upload the above `auth_params.json` to Jupyter Notebook.
3. Execute the following python code to get the token (Replace `<AWS_REGION>` and the `<COGNITO_CLIENT_ID>` with the correct values).

```python
import boto3
import json

client = boto3.client('cognito-idp', region_name='<AWS_REGION>')

# Read auth_params.json file
auth_file = open("auth_params.json")
auth_params = json.load(auth_file)

# Get tokens from Cognito
response = client.initiate_auth(
    AuthFlow='USER_PASSWORD_AUTH',
    AuthParameters=auth_params,
    ClientId='<COGNITO_CLIENT_ID>'
)

# Print all tokens
print(response)

# Print access token
access_token = response['AuthenticationResult']['AccessToken']
print(access_token)
```

### Results

The both of above approaches will return a response similar the following response with a `access_token`, `id_token` and a `refresh_token` for the given user.

```json

"AuthenticationResult": {
        "AccessToken": "eyJsdhjdsjjkkjkjhjk.........",
        "ExpiresIn": 3600,
        "TokenType": "Bearer",
        "RefreshToken": "eyJjwdwd............",
        "IdToken": "eyGhjwm....."
}
```

### Additional Notes

If you try to get a token for an App Client, which has a client secret, then you will see an error similar to the following error.

```
An error occurred (NotAuthorizedException) when calling the InitiateAuth operation: Client 12zc345vvxcv232678vcxv90 is configured for secret but secret was not received
```

To resolve this, please add SECRET\_HASH also as one of the AuthParameters, in addition to the USERNAME and PASSWORD.

The SECRET\_HASH can be calculated as explained in the following page. https://aws.amazon.com/premiumsupport/knowledge-center/cognito-unable-to-verify-secret-hash/

After that, you can use SECRET\_HASH as shown in the following examples.

Example 1:

```
aws cognito-idp initiate-auth --region us-west-2 --auth-flow USER_PASSWORD_AUTH --client-id 12zc345vvxcv232678vcxv90 --auth-parameters USERNAME=user1,PASSWORD=Changeme528*,SECRET_HASH=Jnd8398hdjnjjsd//Ghjskkcdksd/r4=
```

Example 2:

```
"AuthParameters" : {
      "USERNAME" : "user1",
      "PASSWORD" : "Changeme528*",
      "SECRET_HASH" : "Jnd8398hdjnjjsd//Ghjskkcdksd/r4="
   },
```
