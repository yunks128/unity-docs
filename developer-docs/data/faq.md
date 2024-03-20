# FAQ

### I am getting an error during stage-in when processing data from a DAAC, What's going on?

In your airflow logs, you might see an error like the following:\
`2024-03-20 16:16:14,540 [WARNING] [cumulus_lambda_functions.lib.earthdata_login.urs_token::81] Error getting the token - check user name and password`

If this happens, you'll usually get an error in the actual processing algorith, and it comes down to the data expected are not present. There are 2 items to check:

1. Make sure that the parameters you're using are up to date, this is, by default, the `/sps/processing/workflows/edl_username` and `/sps/processing/workflows/edl_password`. If these are incorrect or out of date, you'll have issues accessing the token.
2. the EDL tokens expire after 90 days. If you login to the EDL profile being used for data download, and go to 'generate tokens', make sure a token exists here- only 2 can be created at any given time!\
   \
   ![View of the "Generate Bearer Token" page in URS.](<../../.gitbook/assets/Screenshot 2024-03-20 at 9.41.42 AM.png>)
