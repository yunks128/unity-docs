# Unity Security Cases

Please use this page to determine which use case best applies to your particular security need. From this, you will be able to identify what technologies will be involved, and what adaptation code (if any) you will need to write.

### App-to-App (service-to-service) Interaction.

In this case, there is a need to call/interact with one service endpoint, from another, in a non-human way.

For example, the "caller program" could be:

* a **Python program** calling a REST API,
* [a Jupyter Notebook making a service call](https://github.com/unity-sds/unity-cs/wiki/Amazon-Cognito-Authentication-with-JupyterHub),
* a web application **backend service** layer, calling a REST API,
* a **JavaScript API call** from a UI page,
* etc..

**Service Area Implementation Requirements:**

* Must implement token refresh API calls in code
* Must implement endpoint call in program(s)

The below diagram shows the high-level use case: ![Screen Shot 2022-05-12 at 1 19 22 PM](https://user-images.githubusercontent.com/47542238/168161095-6539f61f-a8b0-423e-b9b0-9d1cf61f0352.png)

### Human-to-UI Interaction.

In this case, a human user is accessing a web page such as:

* HySDS UI
* JupyterHub
* or other web UI..

If the user doesn't already have a valid token, then they are required to log in via the Cognito login page, before accessing the target web page.

**Service Area Implementation Requirements:**

* Simple code (e.g. JavaScript) needs to be added on the web application that does a redirect to Cognito. This code will also store the received tokens in the web browser.
* In the case of JupyterHub, it already has built-in logic that does the redirection. ![Screen Shot 2022-05-13 at 9 48 20 AM](https://user-images.githubusercontent.com/47542238/168330803-4d6977d7-814a-4efa-8be4-5aa3d5527010.png)

### Reverse Proxy Interaction.

In this case, a [reverse proxy](https://en.wikipedia.org/wiki/Reverse\_proxy) sits in front of a web application, and controls access to the application.

**Service Area Implementation Requirements:**

* A reverse proxy needs to be deployed.
* If specific resources need specific access control rules, then these rules can be added in the reverse proxy (configuration TBD). ![Screen Shot 2022-05-13 at 9 47 29 AM](https://user-images.githubusercontent.com/47542238/168330505-afea123c-3df4-44ce-8947-57c3139e017e.png)

.
