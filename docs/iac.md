
# Infrastructure as Code

IaC automates the creation and initialization of many services in a cluster or system. 
It runs once and maintains a state of the system.  Kubernetes and other platforms are more reactive though.  
They constantly monitor things and can correct the state in various scenarios.  
Like if a container or node crashes, if traffic goes way up or down, if a disk runs out, etc.  
it can react and make the needed changes.

## Terraform:

- Infrastructure as code.
- **Providers:** a set of resources and Terraform plugins that enable it to manage certain resources.
- **Workflow:** write, plan, apply.  Write defines resources.  Plan compares current state to desired and produces a plan of changes.  
  It knows if certain steps need to happen before other steps.  Apply does the change.
- Uses a state file to keep track of the current state of your resources.
- Order of resources in files does not matter, it discovers relationships and dependencies.
- Is a declarative language, you can have multiple files in one configuration.
- Basic syntax: `<block type> <label> [<more labels>...] { key = value … }`
- The block type “resource” defines infrastructure, and the main purpose of terraform is to define these resources.  
  Other block types just exist to make that easier.
- Values can be expressions, referring to other things.
- Block labels should be quoted, there can be multiple.
- **terraform init:** downloads providers.  Produces .terraform.lock.hcl, keep in gh.
- **terraform plan:** decides what changes need to be made.
- Other commands: validate, fmt, 
- terraform apply: executes the plan.
- For a resource definition, the first label is the type, the second is its name.  
  Collectively these form a unique identifier you can refer to elsewhere.
- State is stored in the file terraform.tfstate
- Run “terraform state list” to see all resources, and some non-resources like data.
- Run “terraform show” for a more verbose and complete view of the state.
- State files may contain secret information, they should not be kept in source control.  They are stored locally by default.  
  HCP Terraform enables a shared secure location to be used.
- Usually we have one “terraform.tf” file which configures terraform itself.  You have a terraform block, 
  containing a required_version and a providers block.  The providers block states what extension providers you need, and their configuration.  
  For example, “aws” is a provider.  This configures the source and version of the provider, but not the provider itself.  
  Configuration for the provider is separate, usually in the main.tf file, within a provider block.  
  The provider block takes one label, the provider type, e.g. “aws”.
- You can define variables, usually in its own “variables.tf” file.  A “variable” block has one label, the variable name.  
  It can have attributes for the description, type, and default value.
- You should have a separate “outputs.tf” file for outputs.
- Refer to variables with `var.<variable_name>`
- You can pass variables by environment variables, command line arguments (to plan), and in files.
- You can define “output” blocks, which take one label, a name.  Its attributes are description and value.  
  The value is an expression referring back to a value from some past built resource.  
  The output will be tracked in the terraform state file.  You can also run “terraform output” to see all output names and their present values.
- You can define modules, which are reusable sets of infrastructure.  To use an existing module, add the “module” block to your configuration, 
  with one label, the name you are assigning it.  Within that, set the source and version of the existing module.  
  The source is a github repository path, if you omit the host then it is assumed to be in the terraform modules. 
  The version is like a tag, it’s unclear to me if it’s a git tag or a sub-directory in its source repository.

## Ansible
