# Secure Token Service

For users without access to an MCP account, the MDPS Admins can generate a short term token that allows specific access to underlying services. This allows interaction for a brief amount of time to do the following:

* Read and Write to the venue S3 data bucket
* Create repositories in ECR
* Read and Write containers to a private ECR Repository.

### Generate a Token

Requirements:&#x20;

* The Admin must have access to the MCP Account via short-term access keys
* The role and associated IAM policies must already be created

to generate the token, the MDPS admin user must first have access to interact with the AWS Resources. These short-term access keys can be generated for the account in question via the MCP Kion dashboard.

Once access has been configured, run the following command:

```
aws sts assume-role --role-arn <role-arn> --role-session-name <random-unique-session-id> --external-id <external-id>
```

* \<role-arn> is the arn of the role for which the user is getting access. This should generally look like `arn:aws:iam:<account-id>:role/unity-<project>-<venue>-sts`
* A \<random-unique-session-id>. This can be anything, but should incldue the name of the user this token is for and a date or time stamp. e.g. unityuser12252025
* The \<external-id> is configured as part of the role itself, and is meant to prevent "confused deputy" problem, in which a user might accidentally request the resources of another project/venue. This is currently not given to the end user (for now), but is used as a secondary, sanity check that we're generating a token for the correct account. The external-id can be found in the IAM role policy document, or in an SSM parameter called `/unity/{project}/{venue}/cs/sts/role/external-id.`Ideally the external-id is provided by the r_equesting user_ to ensure access to the role_._

The result of the call is a json object with a few items:

```
{
    "AssumedRoleUser": {
        "AssumedRoleId": "AROA3XFRBF535PLBIFPI4:s3-access-example",
        "Arn": "arn:aws:sts::123456789012:assumed-role/xaccounts3access/s3-access-example"
    },
    "Credentials": {
        "SecretAccessKey": "9drTJvcXLB89EXAMPLELB8923FB892xMFI",
        "SessionToken": "AQoXdzELDDY//////////wEaoAK1wvxJY12r2IrDFT2IvAzTCn3zHoZ7YNtpiQLF0MqZye/qwjzP2iEXAMPLEbw/m3hsj8VBTkPORGvr9jM5sgP+w9IZWZnU+LWhmg+a5fDi2oTGUYcdg9uexQ4mtCHIHfi4citgqZTgco40Yqr4lIlo4V2b2Dyauk0eYFNebHtYlFVgAUj+7Indz3LU0aTWk1WKIjHmmMCIoTkyYp/k7kUG7moeEYKSitwQIi6Gjn+nyzM+PtoA3685ixzv0R7i5rjQi0YE0lf1oeie3bDiNHncmzosRM6SFiPzSvp6h/32xQuZsjcypmwsPSDtTPYcs0+YN/8BRi2/IcrxSpnWEXAMPLEXSDFTAQAM6Dl9zR0tXoybnlrZIwMLlMi1Kcgo5OytwU=",
        "Expiration": "2016-03-15T00:05:07Z",
        "AccessKeyId": "ASIAJEXAMPLEXEG2JICEA"
    }
}
```

The entire json should be sent to a user via LFT or some other secure, encrypted email service. **Do not send this via slack or email**, as it is sensitive information.







