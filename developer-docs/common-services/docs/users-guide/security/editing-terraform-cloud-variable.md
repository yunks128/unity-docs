# Editing terraform cloud variable

Terraform Cloud supports variables in the same way that GitHub supports secrets. Our variables are set up as read-only environment variables, and applied to workspaces as 'sets'

A Variable Set is a group of variables that can be applied to multiple workspaces without the need to update each workspace. At the moment our AWS access keys are stored in variable sets in Terraform Cloud.
