# Terraform Cheat sheet
## A Cheat sheet for terraform with AWS and related topics

### Syntax - Basic
```hcl
resource "aws_instance" "basic-ec2-server" {
    ami           = "ami-0dc2d3e4c0f9ebd18"
    instance_type = "t2.micro"

    tags = {
    #   Name = "Ubuntu"
    }
}
```
An EC2 instance

```hcl
resource "aws_vpc" "main" {
  cidr_block = "10.0.0.0/16"
}
```
A VPC in AWS