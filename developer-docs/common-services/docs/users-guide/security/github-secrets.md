# Github Secrets

The Unity CS Team use github secrets to store sensitive API keys and tokens for working with cloud services.

Currently we store our Terraform Cloud API Key inside of a GitHub Secret which is configured to be only accessible to the unity-cs-infra repository. The value is write-only, so while it may be used by GitHub Actions to communicate with Terraform Cloud it cannot be read by users.
