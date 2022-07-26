# Creating Terraform Scripts

You will need to create your own [Terraform](https://www.terraform.io/) scripts, that will be leveraged by the Unity CS deployment framework.

Your scripts will:

* Need to reside in a `terraform-unity` directory at the top-level of your GitHub repository.
  * For example: `unity-sds/unity-sps-prototype/terraform-unity`
  * `main.tf will be the main entry point into the terraform scripts`
  * ``[Example from U-SPS](https://github.com/unity-sds/unity-sps-prototype/tree/main/terraform-unity)
  * sub-directories under `terraform-unity` can also exist, and these terraform scripts will also be picked up, if linked to directly via terraform modules in the terraform files in `terraform-unity`
* can reference Unity CS core/shared variables like `vpc_id`.
  * TODO: enumerate what these variables are, and what they can be used for.

You can create terraform scripts with any name, other than the ones U-CS.

TODO: enumerate what reserved script names there are, so that they can be avoided.
