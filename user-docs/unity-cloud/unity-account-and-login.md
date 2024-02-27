---
description: >-
  This page describes the Unity Account, how to log in, and how to reset your
  password.
---

# Unity Account and Login

### Logging in for the first time (test or development systems)

Currently it necessary for a Unity administrator to create a new account for you. Please contact your Unity support team to request an account.

1. When a new account is created you will receive an email with a temporary password. It will come from `no-reply@verificationemail.com`. If you did not receive this email please let the support team know.
2. Go to the Jupyter URL ([https://jupyter-venue-test-alb-92992455.us-west-2.elb.amazonaws.com:8000/](https://jupyter-venue-test-alb-92992455.us-west-2.elb.amazonaws.com:8000/)). You will likely see a notice that there is a "security risk". This is because we self-sign the security certificates in the development environment and is expected.  \
   ![](../../.gitbook/assets/login-1-security-risk.png)
3. "Accept the Risk" as per your web browser's standard method. Firefox is shown here as an example. On Chrome you will need to click on the text link and then accept.\
   ![](../../.gitbook/assets/login-2-accept-risk.png)
4. You should see a button that says "Log in with Unity Common Services".  Press the button.\
   ![](<../../.gitbook/assets/login-3-orange-button (1).png>)
5. Now you will have the login prompt. Use the username and password that were emailed to you. \
   ![](../../.gitbook/assets/login-4-user-pass.png)
6. You should be immediately asked to change your password. Please do so.
7. After you have input your login information, Jupyter will ask you to choose "Server Options". If you choose the Minimal environment it will not have many basic tools installed. We suggest using the Sounder SIPS Development Environment.\
   ![](../../.gitbook/assets/login-5-server-options.png)
8. Once you've chosen the server options, Juypter will load. \
   ![](../../.gitbook/assets/login-6-jupyter-loading.png)

### Reset your Password

If you forget your password or need to reset it for some reason:

1. Go to the login page for the environment that you want to work in, e.g. [https://unitysds-test.auth.us-west-2.amazoncognito.com/login?client\_id=59hp9flt6hm7k2nvacqhudqpl2\&response\_type=code\&scope=email+openid+profile\&redirect\_uri=https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/hub/oauth\_callback](https://unitysds-test.auth.us-west-2.amazoncognito.com/login?client\_id=59hp9flt6hm7k2nvacqhudqpl2\&response\_type=code\&scope=email+openid+profile\&redirect\_uri=https://jupyter-test-alb-1633047587.us-west-2.elb.amazonaws.com:8000/hub/oauth\_callback)
2. Select the "Forgot your password?" option, then
3. Enter your user name and press the button "Reset my password".&#x20;
4. That will send a code to your email, which can be used to change the password.

![](<../../.gitbook/assets/Screen Shot 2022-08-10 at 11.27.13 AM.png>)\
![](<../../.gitbook/assets/Screen Shot 2022-08-10 at 11.28.22 AM (1).png>)\
![](<../../.gitbook/assets/Screen Shot 2022-08-10 at 11.28.40 AM.png>)

