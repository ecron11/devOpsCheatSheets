# Terraform Cheat sheet
## A Cheat sheet for terraform commands and terraform related topics

### Commands - Basic
```bash
terraform init
```
initializes any terraform and downloads any providers in config file

```bash
terraform plan
```
Does a "dry run" of code. Shows what will be done when config is created

```bash
terraform apply
```
Runs terraform code to match state to terraform file

```bash
terraform destroy
```
Removes configuration managed by config file

```bash
terraform state list
```
lists current terraform state

```bash
terraform state show <resource.name>
```
shows the state of a specific resource

```bash
terraform refresh
```
refreshes the terraform state

```bash
terraform destroy -target <resource-name>
```
Destroys only targeted resource

```bash
terraform apply -var "<variable>=<variable value>"
```
Runs apply and sets a variable via command line

```bash
terraform apply -var-file "<filename>.tfvars"
```
Runs apply and sets varsudo chown -R elonguepee /usr/bin/codeiables based on a specific var file

```bash
terraform output
```
Shows all of the output variables
Can specify a specific variable to get value of that variable specifically

```bash
terraform validate
```
Validate syntax of terraform file

```bash
terraform fmt
```
Formats hcl to make more readable

```bash
terraform show
```
Shows state of terraform
-json for json

```bash
terraform graph
```
Shows dependency graph

```bash
terraform state mv <source> <destination>
```
Can be used to move resources from one state file to another or to rename resources

```bash
terraform state pull
```
Used to pull the state down from a remote

```bash
terraform state rm <resource name>
```
Used to remove a resource from the state so it is no longer managed. The resource is not destroyed, it is just no longer tracked by terraform

```bash
terraform taint <resource name>
```
Taints a specified resource

```bash
terraform untaint <resource name>
```
Untaints a specified resource

```bash
terraform import <resource type>.<resource_name> <unique identifier for resource>
```
Imports an existing resource into terraform state

```bash
terraform console
```
Opens the interactive terraform console. Loads state in the terraform directory by default. Can run functions here to see output

```bash
terraform workspace new <workspace Name>
```
Creates a new workspace and switches to it

```bash
terraform workspace select <workspace Name>
```
Switches to a different workspace

### Syntax - Basic
```hcl
resource "<provider>_<resource_type>" "<name>" {
    <config>=<options>
}
```
Basic resource syntax. Name is just scoped to terraform.

```hcl
resource "aws_vpc" "test-vpc" {
  cidr_block = "10.0.0.0/16"
  tags = {
    "Name" = "test"
  }
}

resource "aws_subnet" "subnet-1" {
  vpc_id     = aws_vpc.test-vpc.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "test-subnet"
  }
}
```
A subnet referencing a vpc for AWS. This demonstrates how to reference properties of resources in a terraform file

```hcl
resource "aws_eip" "apache-server-eip" {
  vpc                       = true
  network_interface         = aws_network_interface.prod-main-interface.id
  associate_with_private_ip = "10.0.1.50"
  depends_on = [
    aws_internet_gateway.production-gw,
    aws_instance.apache-server
  ]
  tags = {
    "Name" = "apache-server-ip"
  }
}
```
An AWS eip resource demonstrating the depends_on syntax and the list syntax

```hcl
output "server_public_ip" {
    value = aws_eip.apache-server-eip.public_ip
}
```
Syntax for output from terraform apply command

```hcl
variable "subnet-prefix" {
  description = "CIDR block for subnet"
  default = "10.0.1.0/24"
}
```
Syntax for variables. User will be prompted for variable

```hcl
cidr_block = var.subnet-prefix
```
syntax to use a variable

```hcl
data "<provider>_<resource_type>" "<name>" {
    <config>=<options>
}
```
syntax for data source

```hcl
variable "example" {
  default = [
    "example1",
    "example2",
    "example3"
  ]
}

resource "<provider>_<resource_type>" "<name>" {
    name = var.example[count.index]

    count = length(var.filename)
}
```
An example using the count meta-argument length function

```hcl
variable "example" {
  type=set(string)
  default = [
    "example1",
    "example2",
    "example3"
  ]
}

resource "<provider>_<resource_type>" "<name>" {
    name = each.value

    for_each = var.example
}
```
An example for each

```hcl
terraform {
  required_providers {
    local = {
      source = "hashicorp/local"
      version = "2.0.0"
    }
  }
}
```
Example syntax for requiring a provider

```hcl
resource "local_file" "file" {
  filename = "/root ${var.filename}"
  content = var.filecontent
}
```
An example of using a variable inline

### Syntax - Intermediate
```hcl
resource "aws_iam_policy" "adminUser" {
  name = "AdminUsers"
  policy = file("admin-policy.json")
}
```
Example syntax for using a file to input into resource

```hcl
resource "aws_iam_policy" "adminUser" {
  name = "AdminUsers"
  policy = <<EOF
  {
    "Version" : "Example"
    "Statement": [
      {
        "Effect": "Allow"
      }
    ]
  }
  EOF
}
```
Example syntax for HEREDOC format to input a file inline.

```hcl
terraform {
  backend "s3" {
    key = "terraform.tfstate"
    region = "us-east-1"
    bucket = "remote-state"
    dynamodb_table = "state-locking"
  }
}
```
Using remote-state with state locking with S3 and DynamoDb

```hcl
<condition> ? <true value> : <false_value> 
```
An if statement in terraform

```hcl
provider "aws" {
  region = "us-east-1"
}
provider "aws" {
  region "us-west-1"
  alias = "west"
}
```
An example of using multiple providers with an alias. In this case, if a resource is allocated with aws_<resource_type>. To use the alias, you would specify "provider = aws.west" in the resource block

```hcl
resource "aws_elastic_beanstalk_environment" "tfenvtest" {
  name                = "tf-test-name"
  application         = "${aws_elastic_beanstalk_application.tftest.name}"
  solution_stack_name = "64bit Amazon Linux 2018.03 v2.11.4 running Go 1.12.6"

  dynamic "setting" {
    for_each = var.settings
    content {
      namespace = setting.value["namespace"]
      name = setting.value["name"]
      value = setting.value["value"]
    }
  }
}
```
An example of a Dynamic block. Lets you do a for loop for nested blocks. Can do with a count too

### Functions

#### Numeric Functions
```hcl
max(<set of numbers>)
```
returns highest from set of numbers

```hcl
min(<set of numbers>)
```
returns lowest from set of numbers

```hcl
min(var.numset...)
```
An example of using the expansion syntax to expand a set

```hcl
ceil(<number>)
```
Returns the closet whole number greater than the argument provided
```hcl
floor(<number>)
```
Returns the closet whole number less than the argument provided

#### String Funtions
```hcl
split(<delimmitter>,<string>)
```
splits a string
```hcl
join(<delimmitter>,<string list>)
```
joins several strings together
```hcl
lower(<string>)
```
converts a string to all lower case
```hcl
upper(<string>)
```
converts a string to all upper case
```hcl
substr(<string>,<starting position inclusive>, <number of characters>)
```
Returns a substring 

#### Collection Functions
```hcl
index(<collection>,<value to find>)
```
Returns a substring
```hcl
element(<collection>,<index>)
```
Finds the element at a certain index in a collection
```hcl
contains(<collection>,<value to find>)
```
Determines whether a collection contains a value

#### Map Functions
```hcl
keys(<map>)
```
Returns a list of just the keys from a map
```hcl
values(<map>)
```
Returns a list of just the values from a map
```hcl
lookup(<map>, <key>)
```
Returns a value corresponding to a specific key 
```hcl
lookup(<map>, <key>, <defaultvalue>)
```
Retruns a value for a specific key, but if value is not present, sets the default value

### Concepts
#### Declarative programming
Terraform coding is done declaratively. Instead of saying how to do create infrastructure, you specify what infrastructure you would like and terraform creates it.

#### Provider
Plugins that allow Terraform to talk to specific APIs. AWS, Azure, etc. When a resource is being declared, the provider is usually the value before the underscore

#### Terraform variables file
Terraform automatically looks for a variables file called terraform.tfvars

#### Variable Types
Variable Type | Example
------------ | -------------
string | "Example"
number | 1
bool | true
any | Can be any data type
list | ["string1", "string2"]
set | Like a list, but cannot contain duplicate elements
map | key1=value1 <br> key2=value2
object | example = { <br> key1 = value1 <br> key2 = value2<br>}
tuple | ["String", 8, false]

#### Life Cycle Rules
Rules that are followed at certain points in a resources lifecylce. Such as create_before_destroy. Which will ensure that a new resource is created to replace the old one before it is destroyed.

#### Data sources
Allow Terraform to read attributes for resources that are provisions outside of terraform's control.

#### Data Sources vs. Resources
A data source can only be read from, while a resources is fully managed by Terraform and can be read, created, modified, etc.

#### For Each Meta argument
Iterates over either a map or a set. Can only iterate over these variable types

#### State Locking
While Terraform is updating configuration of a state, another update operation cannot run. This prevents state from running multiple times against the same configuration.

#### Remote Backend
A remote backend refers to storing the state of a terraform configuration on a remote file storage such as Amazon S3. When a remote backend is configured, terraform will reference and update it when doing operations. This prevents different operations from being run simultaneously.

#### Provisioner Behavior
By default a provisioner executes when a resource is created. This can be changed by adding arguments to provisioner blocks such as when = destroy to have the provisioner run when a resource is destroyed.

#### Tainted Resources
Tainted resources are resources that, for some reason are considered invalid and will be recreated by terraform next time a configuration is applied.

#### Logging/debugging in terraform
To see logs in terraform, you can set the TF_LOG environment variable. There are 5 levels of log for more or less information. To export the log to a specific file path, TF_LOG_PATH can be set.

#### Modules
A grouping of terraform files that can be referenced from other terraform files. This is used to make terraform code more re-usable

#### Parent/Child Modules
The directory where you run terraform commands from is considered the parent module

#### Workspaces
For creating multiple states within the same directory