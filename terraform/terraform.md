# Terraform

## Use-case

1. Managing current infrastructure (as code, which has the advantages of app-dev workflow, i.e. [raise PR -> automated testing -> review (plan) -> merge -> deploy (apply)](https://youtu.be/Ce4Q1xLDNjE))
2. Replicating infrastructure (for multiple environments workflow)

## [Fundamental concepts](https://learn.hashicorp.com/collections/terraform/cli) (see the *fundamentals* aside)

CLI, config language, modules, provision, and state.

## [A simple workflow for deployments](https://www.cloudskillsboost.google/focuses/15842?parent=catalog)

1. Scope
    - Confirm what resources need to be created for a given project.
2. Author
    - Create the configuration file in HCL based on the scoped parameters.
3. Initialize
    - Run terraform init in the project directory with the configuration files. This will download the correct provider plug-ins for the project.
4. Plan & Apply
    - Run terraform plan to verify creation process and then terraform apply to create real resources as well as the state file that compares future changes in your configuration files to what actually exists in your deployment environment.

## Workflow details

- You interact with terraform via the `terraform` CLI
- Create a config file named `main.tf`. It's the entry point for terraform. Resources are defined there as code, it's called `Infrastructure as Code`
- The config file is written in several blocks of `terraform` setup (version, backend, and required providers), `providers` settings (project, region, credentials), and `resources` definition (properties varies for each resources)
- To start a new configuration session, run `terraform init`. It will create a lock file and initialize terraform setup
- See planned deployments by running `terraform plan`. Unlike terraform apply, the plan command will only show what would be changed, and never actually apply the changes directly
- Apply configuration by running `terraform apply`. It will print out the resources plan that's going to be deployed, and ask whether to continue with deployment. This is the step where checks should be done to determine whether the deployment looks correct
- Use `terraform show` to see deployment details
- Adding more resources is as simple as editing the `main.tf` file and running `terraform apply` once again
- Deployed infra has no additional software or config applied. To provision the infra, define a `provisioner` block within the resource block in the config file
- Provisioners only run when a resource is created, but adding a provisioner does not force that resource to be destroyed and recreated. Use `terraform taint <resource_type.resource_name>` to recreate the instance

## Stuffs I find interesting

- Resources can reference other resources by calling it's `type` and `name`
- Some resource property blocks can be added even with empty arguments to provide functionalities (e.g. `access_config` in compute resource ensure the resource are accessible from the internet)
- The prefix `~` when resources plan are printed out means the resource are modified in-place (a *non-descructive* change; descructive change are changes that requires provider to replace the existing resource rather than updating it)
- `terraform destroy` is used to erase deployed configurations. It's rarely used, except when dealing with multiple deployment environments (testing, staging, production)
- You can save plan by running `terraform plan -out <plan_name>`. Saving the plan this way ensures that you can apply exactly the same plan in the future (by running `terraform apply <plan_name>`)
- The order that resources are defined in a terraform configuration file has no effect on how Terraform applies changes. So, organize the configuration files in a way that makes the most sense for you

