# Terraform Associate Study - IN PROGRESS

This is a study guide for the [HashiCorp Terraform Associate certification](https://www.hashicorp.com/certification/terraform-associate), bringing together the documentation provided by HashiCorp and my own notes.

This material doesn't cover the contents of the exam; it is instead based off of the study guide and exam review provided by HashiCorp.

Study Guide: [Study Guide - Terraform Associate Certification | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/associate-study?in=terraform/certification)  
Exam Review: [Exam Review - Terraform Associate Certification | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/associate-review?in=terraform/certification)  
Sample Questions: [Sample Questions - Terraform Associate Certification | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/associate-questions?in=terraform/certification)

## 1. Understand Infrastructure as Code (IaC) concepts

### 1a. What is IaC?

[Infrastructure as Code (IaC)](https://en.wikipedia.org/wiki/Infrastructure_as_code) is describing computing infrastructure through a written high-level syntax.

As well as [Terraform](https://www.terraform.io/), other IaC tools include [AWS CloudFormation](https://aws.amazon.com/cloudformation/), [Pulumi](https://www.pulumi.com/) and [Azure Resource Manager templates](https://docs.microsoft.com/en-us/azure/azure-resource-manager/management/overview).

Infrastructure as Code enables numerous benefits including;

- Versioning of your infrastructure - See what your infrastructure was like at a point in time, and the change that have been made.
- Reusable Code - Separate distinct sets of identical infrastructure can be built from the same code.
- Code Modularity - Components can be broken down into discrete, composable pieces that be reused in different situations.
- Shareable Infrastructure - Infrastructure designs can be shared in Open Source and Inner Source repositories, to allow others to reuse the same code.

Terraform can take advantage of all of these benefits.

#### Reading Material

- [Docs - Introduction to Terraform](https://www.terraform.io/intro/index.html#infrastructure-as-code)
- [Blog - Infrastructure as Code in a Private or Public Cloud](https://www.hashicorp.com/blog/infrastructure-as-code-in-a-private-or-public-cloud)

### 1b. Describe advantages of IaC patterns

Infrastructure as Code allows for several benefitial patterns to be used;

- Service APIs (Application Programming Interfaces) can be consumed to utilise Cloud and On-Premises resources.
- Trivial to consume elasticly scalable cloud resources repeatedly.
- Version Control Systems can be used for historical tracking and control of code.
- Continuous Integration and Continuous Deployment (CI/CD) can be implemented to test and deploy your infrastructure.
- Code as documentation - Not only does your code deploy the infrastructure, it also describes how it all joins together.

#### Documentation

[What is infrastructure as code and why is it important?](https://www.hashicorp.com/resources/what-is-infrastructure-as-code)
[Infrastructure as Code in a Private or Public Cloud](https://www.hashicorp.com/blog/infrastructure-as-code-in-a-private-or-public-cloud)

#### Tutorial

[Introduction to Infrastructure as Code with Terraform | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code)

---

## 2. Understand Terraform's purpose (vs other IaC)

### 2a. Explain multi-cloud and provider-agnostic benefits

#### Documentation

[Use Cases - Terraform by HashiCorp](https://www.terraform.io/intro/use-cases.html#multi-cloud-deployment)

#### Notes

- Spreading infrastructure across clouds to increase fault tolerance
  - Don't have to be reliant upon a single region or even a single cloud
- Provider-agnostic design allows for a single configuration to provision resources across multiple platforms
  - Simplifying management, by keeping it under the same

### 2b. Explain the benefits of state

#### Documentation

[State - Terraform by HashiCorp](https://www.terraform.io/docs/state/purpose.html)

#### Tutorial

[Introduction to Infrastructure as Code with Terraform | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/infrastructure-as-code)

#### Notes

- Necessary for Terraform to function
- A source of truth to know if things have changed or not

##### Mapping Real Objects to Terraform Resources

- Need a database of what to map Terraform data to in the real world
- Has to be agnostic; not all providers support tagging or other concepts
  - Even within the providers, not all resources necessarily support tagging
- Each distinct state object needs to be mapped to a distinct resource, to avoid ambiguous data

##### Metadata

- Metadata such as dependencies needs to be tracked
- Typically configurations are used to determine the dependency order
- However, when you delete a resource, it will disappear from the configurations however
  - Need the state to know that the resources need deleting
- Having the state means that order of deletion can also be determined
- Other methods would increase the complexity, especially given that deletions across providers may also need to be performed

##### Performance

- State allows for the attribute values to be cached
- Refreshing the state can be made optional to speed up Terraform for large state files

##### Syncing

- Remote state allows for everyone to operate on the same state at the same time
- Implementing locking means that you’re always going to have the latest state to work with

---

## 3. Understand Terraform basics

### 3a. Handle Terraform and provider installation and versioning

#### Documentation

[Provider Configuration - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/providers.html)
[Terraform Settings - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/terraform.html)

#### Tutorial

[Install Terraform | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/install-cli)
[Build Infrastructure– Providers](https://learn.hashicorp.com/tutorials/terraform/aws-build#providers)

#### Notes

- Terraform releases are available on the Terraform website
- Homebrew tap contains the latest release as well
- Providers are requested via the `provider` block in the root module.
- `terraform init` will download them
- Can also define them in the `terraform` block within `required_providers`

```
terraform {
  required_providers {
    aws = {
      version = ">= 2.7.0"
      source = "hashicorp/aws"
    }
  }
}
```

- No longer recommended to set the provider version at the `provider` block level
- The name defined in the `required_providers` block is the [local name](https://www.terraform.io/docs/configuration/provider-requirements.html#local-names) of the provider, which the `provider` block refers to
  - You should try to use the preferred local name, which prefixes the resource names, unless necessary, to avoid confusion

### 3b. Describe plug-in based architecture

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build#initialize-the-directory)

#### Notes

- Plugin architecture where providers are downloaded and installed to allow access to various services
- Providers are installed during the `terraform init` phase
- Plugins installed to the `.terraform` folder

### 3c. Demonstrate using multiple providers

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build#providers)

#### Notes

- Define multiple `provider` blocks in the configurations to set them up
- Define them all in `required_providers` block as well, just like when you define a single provider
- Resource types are aligned to the specific providers, so automatically use the providers they come from
- Don’t forget about [local names](https://www.terraform.io/docs/configuration/provider-requirements.html#local-names) and how those relate to them

### 3d. Describe how Terraform finds and fetches providers

#### Documentation

[Provider Configuration - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/providers.html)

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build#providers)

#### Notes

- By default, Terraform will look to the Terraform Registry for providers
- However, you can define a source in the `required_providers` block to pull specific providers from certain locations
- This is defined in the `source` parameter as [described here](https://www.terraform.io/docs/configuration/provider-requirements.html#source-addresses)
- You also define the [version constraint](https://www.terraform.io/docs/configuration/provider-requirements.html#version-constraints) to select a specific version of the provider

```
terraform {
  required_providers {
    local_name = {
    source = [<HOSTNAME>/]<NAMESPACE>/<TYPE>
    version =
    }
  }
}
```

### 3e. Explain when to use and not use provisioners and when to use local-exec or remote-exec

#### Documentation

[Provisioners - Terraform by HashiCorp](https://www.terraform.io/docs/provisioners/#provisioners-are-a-last-resort)

#### Tutorial

[Cloud Init](https://learn.hashicorp.com/tutorials/terraform/cloud-init)
[Provision Infrastructure with Packer | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/packer)

#### Notes

- Provisioners are now considered a last resort for Terraform
- Instead, use infrastructure provisioning tools like Packer and cloud-init, user data or metadata, and other tooling like Chef, etc
- Provisioners require login rights to instances, which you really want to avoid for a reliable build and to remove direct network access to instances
- If you don’t have support in a provider, then that may be a good reason to use `local-exec` as a temporary workaround

---

## 4. Use the Terraform CLI (outside of core workflow)

### 4a. Given a scenario: choose when to use terraform fmt to format code

#### Documentation

[Command: fmt - Terraform by HashiCorp](https://www.terraform.io/docs/commands/fmt.html)

#### Notes

- Best to use `terraform fmt` when you’re writing code yourself
- The canonical format can change between terraform versions, so you may need to re-run if upgrading
- Tools that generate Terraform configurations will generate it to this style, so it’s best to follow the canonical style where ever possible

### 4b. Given a scenario: choose when to use terraform taint to taint Terraform resources

#### Documentation

[Command: taint - Terraform by HashiCorp](https://www.terraform.io/docs/commands/taint.html)

#### Notes

- `taint` only impacts the state, it doesn’t modify the infrastructure until terraform is next applied
- Might want to use taint to force a provisioner to run
- May cause other resources to rebuild if they are dependent upon it
- Could be good to use if some resource attributes are not seen by Terraform

### 4c. Given a scenario: choose when to use terraform import to import existing infrastructure into your Terraform state

#### Documentation

[Command: import - Terraform by HashiCorp](https://www.terraform.io/docs/commands/import.html)

#### Notes

- Imports an existing resource into Terraform
- Useful when you have existing resources that you don’t want to or cannot rebuild at that point managed by Terraform

### 4d. Given a scenario: choose when to use terraform workspace to create workspaces

#### Documentation

[State: Workspaces - Terraform by HashiCorp](https://www.terraform.io/docs/state/workspaces.html)

#### Notes

- This is discussing CLI workspaces, not Terraform Cloud/Enterprise Workspaces
- Remember; you’re using a single configuration with a single backend, but then slotting multiple states into that one backend
- Could be using feature branches to map to workspaces, for changes to infrastructure code
- Good for testing changes, so there is a primary workspace, with other workspaces for that testing
- Workspaces not great for avoiding tight coupling; system composition would be better done through a combination of strategies including separating your configurations out with clear boundaries

### 4e. Given a scenario: choose when to use terraform state to view Terraform state

#### Documentation

[Command: state - Terraform by HashiCorp](https://www.terraform.io/docs/commands/state/index.html)

#### Notes

- Use the `state` command instead of interrogating terraform state files directly where possible
- Much safer to use than handling things manually where you can corrupt the files
- Can use it to manage and change state details of resources;
  - `list` list resources within the state file
  - `mv` to move items around in the state
  - `pull` to download a remote state
  - `push` to replace a remote state
  - `replace-provider` to replace the provider for a specific resource in state
  - `rm` remove items from the state; will not remove the underlying resource
  - `show` show the attributes of an item within state

### 4f. Given a scenario: choose when to enable verbose logging and what the outcome/value is

#### Documentation

[Debugging - Terraform by HashiCorp](https://www.terraform.io/docs/internals/debugging.html)

#### Notes

- Use `TF_LOG` to set log levels, so `TRACE, DEBUG, INFO, WARN or ERROR`
- `TF_LOG_PATH` to push the log to a specific location
- Good to provide logs in case of a bug within Terraform for error reporting
- `crash.log` will be written out on a panic error, doesn’t need `TF_LOG` to be set

---

## 5. Interact with Terraform modules

### 5a. Contrast module source options

#### Documentation

[Finding and Using Modules from the Terraform Registry - Terraform by HashiCorp](https://www.terraform.io/docs/registry/modules/use.html)

#### Tutorial

[Modules Overview | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/module)

#### Notes

- Modules can be sourced locally or from remote sources
- Can pull modules from the public Terraform repository or use private repositories
- Public modules can be listed as “verified” where HashiCorp has validated them
  - Not actively maintained by HashiCorp themselves, but rater partners
- Local Paths, Terraform Registry, GitHub, BitBucket, Git/Mercurial, HTTP, S3 buckets, GCS buckets
- Good to use versioning where possible
- HTTP, GCS, S3 can pull down compressed files as well if supported versions

### 5b. Interact with module inputs and outputs

#### Tutorial

[Use Modules from the Registry | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/module-use)

#### Notes

- Try using modules!
- Awareness of defined output and input variables, how to pass them into the modules themselves

### 5c. Describe variable scope within modules/child modules

#### Documentation

[Input Variables - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/variables.html)
[Modules - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/modules.html#calling-a-child-module)

#### Notes

- Variables scoped within modules
- Have to explicitly pass them out from modules via outputs

### 5d. Discover modules from the public Terraform Module Registry

#### Documentation

[Terraform Registry](https://www.terraform.io/docs/registry/modules/use.html)

#### Tutorial

[Use Modules from the Registry | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/module-use#use-the-terraform-registry)

#### Notes

### 5e. Defining module version

#### Documentation

[Modules - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/modules.html#module-versions)

#### Notes

- Use the `version` parameter
- If using VCS sources, you might have to set it within the `source` parameter instead

---

## 6. Navigate Terraform workflow

### 6a. Describe Terraform workflow ( Write -> Plan -> Create )

#### Documentation

[The Core Terraform Workflow - Guides - Terraform by HashiCorp](https://www.terraform.io/guides/core-workflow.html)

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build)

#### Notes

### 6b. Initialise a Terraform working directory (terraform init)

#### Documentation

[Command: init - Terraform by HashiCorp](https://www.terraform.io/docs/commands/init.html)

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build#initialize-the-directory)

#### Notes

### 6c. Validate a Terraform configuration (terraform validate)

#### Documentation

[Command: validate - Terraform by HashiCorp](https://www.terraform.io/docs/commands/validate.html)

#### Notes

### 6d. Generate and review an execution plan for Terraform (terraform plan)

#### Documentation

[Command: plan - Terraform by HashiCorp](https://www.terraform.io/docs/commands/plan.html)

#### Notes

### 6e. Execute changes to infrastructure with Terraform (terraform apply)

#### Documentation

[Command: apply - Terraform by HashiCorp](https://www.terraform.io/docs/commands/apply.html)

#### Tutorial

[Build Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-build#create-infrastructure)

#### Notes

### 6f. Destroy Terraform managed infrastructure (terraform destroy)

#### Documentation

[Command: destroy - Terraform by HashiCorp](https://www.terraform.io/docs/commands/destroy.html)

#### Tutorial

[Destroy Infrastructure | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-destroy)

#### Notes

---

## 7. Implement and maintain state

### 7a. Describe default local backend

#### Documentation

[Backends - Terraform by HashiCorp](https://www.terraform.io/docs/backends/index.html)
[Backend Type: local - Terraform by HashiCorp](https://www.terraform.io/docs/backends/types/local.html)

#### Notes

- Defaults to writing the state out to file
- Uses system APIs to lock the state
- Can set the path

### 7b. Outline state locking

#### Documentation

[State: Locking - Terraform by HashiCorp](https://www.terraform.io/docs/state/locking.html)

#### Notes

- Locks the state for all operations that could write
- Prevents others from locking the state and causing corruption
- Does not show a message if it happens
  - Shows a message if the lock fails
- Can optionally disable locking, but not recommended
- If it takes time to find the lock, it will show a message
- Not all backends support locking; it is on a per-backend basis
- You can force-unlock a lock with the `terraform force-unlock` command
  - Requires the Lock ID which is shown during the lock failure error message

### 7c. Handle backend authentication methods

#### Documentation

[Backend: Supported Backend Types - Terraform by HashiCorp](https://www.terraform.io/docs/backends/types/index.html)

#### Notes

- Different backends require different authentication methods, detailed in their documentation
- Typically, it’s specific to how the backend’s platform handles authentication;
  - artifactory supports usernames and passwords
  - azurerm (Azure Blob Storage) supports several different auth methods
    - Managed Service Identity
    - SAS Token
    - Storage Account Access Key
    - Service Principal with Client Certificate
    - Service Principal with Client Secret
  - consul supports username/password over Basic HTTP auth and certificate based auth
  - cos (Tencent Object Storage) supports secret ID and key auth
  - etc supports username/password auth
  - etcdv3 supports username/password or certificate based auth
  - gcs (Google Cloud Storage) supports JSON credentials or access tokens
  - http supports HTTP basic username/password auth
  - kubernetes uses secrets and supports HTTP basic username/password auth, certificate auth and service account tokens
  - manta (Joyent Triton) supports private key based auth
  - oss (Alibaba Object Storage Service with Cloud Table Store for locking) supports RAM roles, access keys, assumed roles and credential files
  - pg (PostgreSQL) supports connection string auth
  - s3 (AWS Simple Storage Service with DynamoDB for locking) requires IAM credentials
  - swift (OpenStack) supports secret, token, certificate and username/password based auth
  - remote (Terraform Cloud/Enterprise) backend supports token based auth

### 7d. Describe remote state storage mechanisms and supported standard backends

#### Documentation

[Backend: Supported Backend Types - Terraform by HashiCorp](https://www.terraform.io/docs/backends/types/index.html)

#### Tutorial

[Store Remote State | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-remote)

#### Notes

### 7e. Describe effect of Terraform refresh on state

#### Documentation

[Command: refresh - Terraform by HashiCorp](https://www.terraform.io/docs/commands/refresh.html)

#### Notes

### 7f. Describe backend block in configuration and best practices for partial configurations

#### Documentation

[Backends: Configuration - Terraform by HashiCorp](https://www.terraform.io/docs/backends/config.html)

#### Tutorial

[Store Remote State | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-remote)

#### Notes

- Partial configuration good for keeping secrets out of your configuration
  - Keep them out of code storage
- No interpolations allowed in the backend configuration however!
- Pass in interactively, via a backend configuration file or via command line options when you want to hide secrets

### 7g. Understand secret management in state files

#### Documentation

[State: Sensitive Data - Terraform by HashiCorp](https://www.terraform.io/docs/state/sensitive-data.html)

#### Notes

- State file can hold sensitive data
- Therefore, treat the state as a whole like it is sensitive data
  - Encrypt in transit and at reset
- Local state is plain text, so use remote state

---

## 8. Read, generate, and modify configuration

### 8a. Demonstrate use of variables and outputs

#### Tutorial

[Define Input Variables | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-variables)
[Query Data with Output Variables | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/aws-outputs)

#### Notes

```
variable "name" {
  default = "value"
  type = string
  description = "What the variable is for"

  validation {
    condition     = ""
    error_message = ""
  }
}
```

### 8b. Describe secure secret injection best practice

#### Documentation

[Terraform Registry](https://www.terraform.io/docs/providers/vault/index.html)

#### Tutorial

[Inject secrets into Terraform using the Vault provider | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/secrets-vault)

#### Notes

### 8c. Understand the use of collection and structural types

#### Documentation

[Type Constraints - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/types.html#complex-types)

#### Notes

### 8d. Create and differentiate resource and data configuration

#### Documentation

[Resources - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/resources.html)
[Data Sources - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/data-sources.html)

#### Notes

### 8e. Use resource addressing and resource parameters to connect resources together

#### Documentation

[Internals: Resource Address - Terraform by HashiCorp](https://www.terraform.io/docs/internals/resource-addressing.html)
[Expressions - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/expressions.html#references-to-named-values)

#### Notes

### 8f. Use Terraform built-in functions to write configuration

#### Documentation

[Functions - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/functions.html)

#### Notes

### 8g. Configure resource using a dynamic block

#### Documentation

[Expressions - Configuration Language - Terraform by HashiCorp](https://www.terraform.io/docs/configuration/expressions.html#dynamic-blocks)

#### Notes

```
dynamic "setting" {
  for_each = var.settings
  content {
    foo = setting.value["foo"]
    bar = setting.value["bar"]
}
```

### 8h. Describe built-in dependency management (order of execution based)

#### Tutorial

[Create Resource Dependencies | Terraform - HashiCorp Learn](https://learn.hashicorp.com/tutorials/terraform/dependencies)

#### Notes

---

## 9. Understand Terraform Cloud and Enterprise capabilities

### 9a. Describe the benefits of Sentinel, registry, and workspaces

#### Documentation

[Sentinel - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/sentinel/index.html)
[Private Module Registry - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/registry/index.html)
[Workspaces - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/workspaces/index.html)

#### Tutorial

[Why Policy as Code?](https://www.hashicorp.com/blog/why-policy-as-code/)
[HashiCorp Terraform Module Registry](https://www.hashicorp.com/blog/hashicorp-terraform-module-registry/)
[Terraform Enterprise Workspaces](https://www.hashicorp.com/resources/terraform-enterprise-understanding-workspaces-and-modules)

#### Notes

### 9b. Differentiate OSS and Terraform Cloud workspaces

#### Documentation

[State: Workspaces - Terraform by HashiCorp](https://www.terraform.io/docs/state/workspaces.html)
[Workspaces - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/workspaces/index.html)

#### Notes

### 9c. Summarise features of Terraform Cloud

#### Documentation

[Home - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/index.html)
[UI/VCS-driven Runs - Runs - Terraform Cloud and Terraform Enterprise - Terraform by HashiCorp](https://www.terraform.io/docs/cloud/run/ui.html)

#### Tutorial

[HashiCorp Terraform: Enterprise Pricing, Packages & Features](https://www.hashicorp.com/products/terraform/pricing)

#### Notes
