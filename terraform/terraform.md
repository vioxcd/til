# Terraform

## Advantages

1. Manages current infrastructure as code, which has the advantages of app-dev workflow, i.e. [raise PR -> automated testing -> review (plan) -> merge -> deploy (apply)](https://youtu.be/Ce4Q1xLDNjE) (enable life-cycle management)
2. Easily replicates infrastructure (for multiple environments workflow)
3. Useful for stack-based deployments
4. State-based approach to track resource changes throughout life-cycle

## [Fundamental concepts](https://learn.hashicorp.com/collections/terraform/cli) (see the *fundamentals* aside)

1. CLI
    - Interact with terraform via the `terraform` CLI
2. Config language
    - Create infrastructure as code using the configuration language 
3. Modules
4. Provider
    - Exposes resource from a specific cloud platform to be used in the configuration. (lists of [providers](https://registry.terraform.io/browse/providers))
5. State
6. Provision

## Declarations

- `terraform`
- `providers`
- `resources`
- `variables` and `locals`

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

- Create a config file named `main.tf`. It's the entry point for terraform. Resources are defined there as code, it's called `Infrastructure as Code`
- The config file is written in several blocks of `terraform` setup (version, backend, and required providers), `providers` settings (project, region, credentials), and `resources` definition (properties varies for each resources)
- To start a new configuration session, run `terraform init`. It will create a lock file based on the terraform setup and installs the required providers
- See planned deployments by running `terraform plan`. Unlike terraform apply, the plan command will only show what would be changed, and never actually apply the changes directly
- Apply configuration by running `terraform apply`. It will print out the resources plan that's going to be deployed, and ask whether to continue with deployment. This is the step where checks should be done to determine whether the deployment looks correct
- Use `terraform show` to see deployment details
- Adding more resources is as simple as editing the `main.tf` file and running `terraform apply` once again
- Deployed infra has no additional software or config applied. To provision the infra, define a `provisioner` block within the resource block in the config file
- Provisioners only run when a resource is created, but adding a provisioner does not force that resource to be destroyed and recreated. Use `terraform taint <resource_type.resource_name>` to recreate the instance

## Stuffs I find interesting

- Resources can reference other resources by calling it's `type` and `name`
- Some resource property blocks can be added even with empty arguments to provide functionalities (e.g. `access_config` in compute resource ensure the resource are accessible from the internet)
- There are two types of change: *non-descructive* which updates the deployed infra in-place, and *descructive* change which destroy and recreates the infra. The prefix `~` when resources plan are printed out means the resource are updated
- `terraform destroy` is used to erase deployed configurations. It's rarely used, except when dealing with multiple deployment environments (testing, staging, production)
- You can save plan by running `terraform plan -out <plan_name>`. Saving the plan this way ensures that you can apply exactly the same plan in the future (by running `terraform apply <plan_name>`)
- The order that resources are defined in a terraform configuration file has no effect on how Terraform applies changes. So, organize the configuration files in a way that makes the most sense for you

## Authentication

Use `gcloud auth application-default login` for google cloud. [More info](https://registry.terraform.io/providers/hashicorp/google/latest/docs/guides/provider_reference#primary-authentication)

OR

You can pass a `credentials` property to the provider block.

## Configuration Language

- Use a `variables.tf` file to create constant (a `locals` block) and variable values that can be referenced in the `main.tf` file
- Provide `default` property to variables that can be used across projects, also if you don't provide one, it'll ask for the value at runtime

## References
