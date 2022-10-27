[![Gitter](https://badges.gitter.im/aztfmod/community.svg)](https://gitter.im/aztfmod/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

# Cloud Adoption Framework landing zones for Terraform - Platform starter template

Microsoft [Cloud Adoption Framework for Azure](https://aka.ms/caf) provides you with guidance and best practices to adopt Azure.

A landing zone is a segment of a cloud environment, that has been preprovisioned through code, and is dedicated to the support of one or more workloads. Landing zones provide access to foundational tools and controls to establish a compliant place to innovate and build new workloads in the cloud, or to migrate existing workloads to the cloud. Landing zones use defined sets of cloud services and best practices to set you up for success.

## :rocket: Getting started: go read the docs!



Refer to the CAF Terraform landing zones documentation available at [GitHub Pages](https://aka.ms/caf/terraform).

![homepage](https://aztfmod.github.io/documentation/img/homepage.png)

## Community

Feel free to open an issue for feature or bug, or to submit a pull request.

In case you have any question, you can reach out to tf-landingzones at microsoft dot com.

You can also reach us on [Gitter](https://gitter.im/aztfmod/community?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge)

## Contributing

This project welcomes contributions and suggestions.  Most contributions require you to agree to a
Contributor License Agreement (CLA) declaring that you have the right to, and actually do, grant us
the rights to use your contribution. For details, visit https://cla.opensource.microsoft.com.

When you submit a pull request, a CLA bot will automatically determine whether you need to provide
a CLA and decorate the PR appropriately (e.g., status check, comment). Simply follow the instructions
provided by the bot. You will only need to do this once across all repos using our CLA.

This project has adopted the [Microsoft Open Source Code of Conduct](https://opensource.microsoft.com/codeofconduct/).
For more information see the [Code of Conduct FAQ](https://opensource.microsoft.com/codeofconduct/faq/) or
contact [opencode@microsoft.com](mailto:opencode@microsoft.com) with any additional questions or comments.

## Trademarks

This project may contain trademarks or logos for projects, products, or services. Authorized use of Microsoft
trademarks or logos is subject to and must follow
[Microsoft's Trademark & Brand Guidelines](https://www.microsoft.com/en-us/legal/intellectualproperty/trademarks/usage/general).
Use of Microsoft trademarks or logos in modified versions of this project must not cause confusion or imply Microsoft sponsorship.
Any use of third-party trademarks or logos are subject to those third-party's policies.

Fixes:
On further investigation I was able to fix the azuread_group errors by editing /tf/caf/configuration/level0/launchpad/azuread_groups.tfvars and changing each instance of owner = [[]] to owner = [] and re-running the plan. This implies a bug generating the tfvars files.

------
I was able to successfully run the plan by editing /tf/caf/configuration/level0/launchpad/keyvaults.tfvars and remming out the object_id for the bootstrap_user in each keyvault. For example:

keyvaults = {
  level0 = {
    name               = "l0"
    resource_group_key = "level0"
    sku_name           = "premium"
    tags = {
      caf_tfstate     = "level0"
      caf_environment = "contoso"
    }

    creation_policies = {
      // <redacted>
      bootstrap_user = {
        # object_id          = ""
        secret_permissions = ["Set", "Get", "List", "Delete", "Purge", "Recover"]
      }
      caf_platform_maintainers = {
        azuread_group_key  = "caf_platform_maintainers"
        secret_permissions = ["Set", "Get", "List", "Delete", "Purge", "Recover"]
      }
      caf_platform_contributors = {
        azuread_group_key  = "caf_platform_contributors"
        secret_permissions = ["Get"]
      }
    }
  }
The first apply failed with a couple of local-exec provisioner errors but re-running the plan and apply succeeded.

-----
After following both these suggestions was able to run rover with the plan and apply steps:
in /tf/caf/configuration/level0/launchpad/azuread_groups.tfvars

changing each instance of owner = [[]] to owner = []

and

in /tf/caf/configuration/level0/launchpad/keyvaults.tfvars

remming out the object_id for the bootstrap_user
