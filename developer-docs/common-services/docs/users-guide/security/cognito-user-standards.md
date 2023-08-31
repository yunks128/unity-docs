# Cognito User Standards

Cognito Users can be created in a Cognito User pool. These Users are used to obtain Cognito auth tokens.

To create a Cognito User, at least following attributes are required. A User can be created in AWS Console (\[Amazon Cognito] -> \[User pools] -> \[unity-user-pool] -> Create user).

* **Invitation message**
  * Make sure to select the option "Send an email invitation", so the user will receive an email to change the password.
* **User name**
  * It is recommended to always use the NASA Agency User ID for this.
  * Must be unique within the user pool.
  * Must be a UTF-8 string between 1 and 128 characters.
  * After the user is created, the username can't be changed.
* **Email address**
  * It is recommended to always use the JPL email address for this.
  * A user's email address can be used for sign-in, account recovery, and account confirmation.
* **Password**
  * It is recommended to generate a password.
  * This temporary password will be sent to the user's email address.
  * The user will be promoted to change the password during the user's first login.
