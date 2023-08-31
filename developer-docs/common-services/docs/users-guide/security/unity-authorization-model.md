# Unity Authorization Model

The Unity authorization capabilities will be controlled by a system where :

* There are **Users** (e.g. identities in one or more of: EDL, JPL LDAP, Google, etc..)
* Once a User is **authenticated**, they are considered to be authenticated for the entire Unity system.
* There are **roles**, that control **authorization** to various Unity resources.
* Roles contain one or more **permissions**
* Users have one or more roles
* Users can be real users (actual human identity), or an App user
* The mapping of roles-to-permissions may be managed by applications themselves (i.e. in application business logic), instead of being centrally stored.
  * (Alternatively, we may explore the possibility of somehow storing this mapping centrally)
* **Applications** use a User’s role(s) to determine what the User is allowed to do (or not do).

Below is a diagram that illustrates the logical relationships between an example set of users, roles, and permissions.

## Logical View:

![Screen Shot 2022-03-22 at 9 49 57 PM](https://user-images.githubusercontent.com/47542238/159626271-6ddfd0ba-1fe5-4453-995b-549aa7a63329.png)

Note that this logical view is a conceptual representation, but when implemented on AWS, it will be structured in terms of AWS Cognito users and groups. Cognito groups, in this case, are used to represent the roles, and the applications will encode business logic dictating how roles can control certain behaviors on the application. The below diagram shows how the above logical example would be structured in AWS resources, and Unity service applications:

## AWS Implementation View (AWS Cognito Users & Groups):

![Screen Shot 2022-03-22 at 9 52 14 PM](https://user-images.githubusercontent.com/47542238/159626493-44a6a71d-25ab-47d5-ad26-b1dbf46b1721.png)

## Authorization Flow

The below diagram shows two possible scenarios where a user's access is controlled via authorizations. Depending on the feasibility of integrating authorization checks/logic into the application itself, it may be necessary to intercept the user requests, and determine accessibility outside of application, in a reverse proxy that intercepts requests:

![Screen Shot 2022-03-22 at 10 08 54 PM](https://user-images.githubusercontent.com/47542238/159628294-d31d8c02-5b39-4a04-9884-f61ec33628b9.png) ![](https://user-images.githubusercontent.com/47542238/159750940-b980b4a4-d1da-40ce-a73a-4aebef1e82ba.png)
