# Amazon Cognito Authentication with JupyterHub

## Integrating Amazon Cognito Authentication with JupyterHub

**Note:** The screenshots in this example shows a JupyterHub setup in localhost. However, the same steps were tested with an AWS EC2 instance too. On AWS, an application load balancer was setup in front of the ED2 insatnce. Then in the step 9 below the `c.OAuthenticator.oauth_callback_url = "http://localhost:8000/hub/oauth_callback"` was changed to AWS ALB URL as `c.OAuthenticator.oauth_callback_url = "https://alb-jupyter-hub-123456.us-westt-2.elb.amazonaws.com/hub/oauth_callback"`.

Amazon Cognito Authentication can be integrated with JupyterHub as follows.

1. Install `JupyterHub` as explained in https://jupyterhub.readthedocs.io/en/stable/quickstart.html.
2. Install `JupyterLab` as explained in https://jupyterlab.readthedocs.io/en/stable/getting\_started/installation.html.
3. Make sure that you have `docker` installed and running on the host machine.
4. Install `oauthenticator` as explained in https://oauthenticator.readthedocs.io/en/latest/install.html.
5. Install `dockerspawner` as follows (Make sure that `pip3` is already installed on the host machine before this step).

```
pip3 install dockerspawner
```

6. Pull the `jupyterhub/singleuser:latest` docker image as follows.

```
docker pull jupyterhub/singleuser:latest
```

7. Generate JupyterHub configurations as follows. This will create a file named `jupyterhub_config.py`.

```
jupyterhub --generate-config
```

8. Open the `jupyterhub_config.py` file.
9. Add or modify the following configurations in `jupyterhub_config.py` file.

```

# c.JupyterHub.hub_bind_url = 'http://0.0.0.0:8081'
# c.JupyterHub.port = 80
# c.JupyterHub.spawner_class = 'dockerspawner.DockerSpawner'
# c.DockerSpawner.image = 'jupyterhub/singleuser:latest'


from oauthenticator.generic import GenericOAuthenticator
c.JupyterHub.authenticator_class = GenericOAuthenticator
c.OAuthenticator.oauth_callback_url = "http://localhost:8000/hub/oauth_callback"

# Please get the correct Unity Cognito user pool client ID and client secret from Unity CS team
c.OAuthenticator.client_id = "<ADD COGNITO CLIENT ID HERE>"
c.OAuthenticator.client_secret = "<ADD COGNITO CLIENT SECRET HERE>"

c.GenericOAuthenticator.login_service = "Unity Common Services"
c.GenericOAuthenticator.username_key = "username"

# Please get the correct Unity Cognito user pool domain name from Unity CS team
c.GenericOAuthenticator.authorize_url = "https://unity.auth.us-west-2.amazoncognito.com/oauth2/authorize"
c.GenericOAuthenticator.token_url = "https://unity.auth.us-west-2.amazoncognito.com/oauth2/token"
c.GenericOAuthenticator.userdata_url = "https://unity.auth.us-west-2.amazoncognito.com/oauth2/userInfo"

# Add list of admin users (additional users can be added through the JupyterHub UI)
c.Authenticator.admin_users = {'awstestuser777'}

# Add list of allowed users (additional users can be added through the JupyterHub UI)
c.Authenticator.allowed_users = {'awstestuser777', 'awstestuser888'}

```

### Additional Notes:

* The oauth\_callback\_url doesn’t have to be visible to Cognito or AWS.
* The oauth\_callback\_url is just a way of telling Cognito that “This is the URL that you should redirect the client’s web browser to, after the authentication“.
* I had http://localhost:8000/hub/oauth\_callback setup as Callback URL in Cognito App Client setup and it allowed me to redirect to my localhost URL.
* When I setup JupyterHub on AWS, I used https://alb-jupyter-hub-123456.us-westt-2.elb.amazonaws.com/hub/oauth\_callback as Callback URL in Cognito App Client setup and it allowed me to redirect to my AWS load balancer URL (in this case I had to use a Application Load Balancer in front of my EC2 instance hosting the JupyetHub, because Cognito needs https URLs for callback URLs other than localhost URLs.
* In Cognito, we can create multiple App Clients, and ideally we should create seperate App Clients for different Callback URLs (E.g. one App Client for hysds\_ui, another App Client for JupyterHub)

10. Start the JupyterHub as follows.

```
jupyterhub -f jupyterhub_config.py --ip=localhost
```

11. Access the URL http://localhost:8000 (the HTTPS can be enabled as explained in https://www.pugetsystems.com/labs/hpc/Note-Self-Signed-SSL-Certificate-for-local-JupyterHub-1749/ )
12. Click on the button **Login with Unity Common Services**.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/jupyterhub/JupyterHub-Home-Page.png)

13. Enter user credentials for a preferred identity provider (E.g.: JPL LDAP, Cognito User Pool) and sign-in.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/jupyterhub/JupyterHub-Cognito-Login-Screen.png)

14. This will create a JupyterLab environment for the logged-in user as follows.

![](https://github.com/unity-sds/unity-cs-security/blob/main/images/jupyterhub/JupyterHub-JupyterLab-Env.png)

15. If it is required to reuse the tokens obtained as a result of above process to call an external RESTful API through a Jupyter Notebook, it is possible to implement an Authenticator as explained in the following page. https://oauthenticator.readthedocs.io/en/latest/writing-an-oauthenticator.html
16. The `access_token` stored in the above step can be used to call a secured RESTful API from the Jupyter Notebook (by adding the `access_token` as the `Authorization` header. This is a human-to-api authentication scenario.
17. To implement an api-to-api use case, the OAuth2.0 Client Credential flow based approach used in the following code sample can be used. https://github.com/unity-sds/unity-cs-security/blob/main/code\_samples/hysds\_mozart\_mock\_consumer/app.py
