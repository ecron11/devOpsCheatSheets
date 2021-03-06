# Terraform Cheat sheet
## A Cheat sheet for terraform with AWS and related topics

### CLI commands
```bash
aws configure
```
This can be used to set your access key and secret key. This will store it locally and will be passed in automatically to terraform so there is no need for storing credentials in variable files

### Syntax - Basic
```hcl
provider "aws" {
  region = "us-east-1"
}
```
Syntax for setting up aws provider. A access key and secret key could be supplied here, but is generally more secure to set it up with the aws configure cli command.

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

```hcl
resource "aws_subnet" "subnet-1" {
  vpc_id     = aws_vpc.test-vpc.id
  cidr_block = "10.0.1.0/24"

  tags = {
    Name = "test-subnet"
  }
}
```
A subnet in AWS referencing a vpc

```hcl
resource "aws_internet_gateway" "production-gateway" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main"
  }
}
```
An internet gateway in aws referencing a vpc

```hcl
resource "aws_route_table" "prod-main-route" {
  vpc_id = aws_vpc.prod-vpc.id

  route {
    cidr_block = "10.0.1.0/24"
    gateway_id = aws_internet_gateway.production-gw.id
  }

  tags = {
    Name = "example"
  }
}
```
A route table with a subnet associated with it


```hcl
resource "aws_network_interface" "test" {
  subnet_id       = aws_subnet.public_a.id
  private_ips     = ["10.0.0.50"]
  security_groups = [aws_security_group.web.id]

  attachment {
    instance     = aws_instance.test.id
    device_index = 1
  }
}
```
A network interface

```hcl
resource "aws_security_group" "allow_tls" {
    name        = "allow_tls"
    description = "Allow tls traffic from anywhere"
    vpc_id      = aws_vpc.prod-vpc.id

    ingress {
        description      = "TLS from VPC"
        from_port        = 443
        to_port          = 443
        protocol         = "tcp"
        cidr_blocks      = ["0.0.0.0/0"]
    }

    egress {
        from_port        = 0
        to_port          = 0
        protocol         = "-1"
        cidr_blocks      = ["0.0.0.0/0"]
        ipv6_cidr_blocks = ["::/0"]
    }

    tags = {
        Name = "allow__all_web_and_ssh"
    }
}
```
A Security group that allows HTTPS traffic from anywhere and outbound traffic. Use from_port and to_port to enable a range of ports
**Note!** By default, AWS adds an outbound rule for all traffic. Terraform removes this rule, and it must be explicitly defined.

```hcl
resource "aws_eip" "one" {
  vpc                       = true
  network_interface         = aws_network_interface.multi-ip.id
  associate_with_private_ip = "10.0.0.10"
}
```
A elastic ip associated with a network interface

```hcl
resource "aws_route_table_association" "a" {
  subnet_id      = aws_subnet.foo.id
  route_table_id = aws_route_table.bar.id
}
```
A resource to associate a subnet with a route table

```hcl
resource "aws_s3_bucket" "example_bucket" {
  bucket = "an_example_bucket"
  acl = "public-read-write"
}
```
An S3 bucket

```hcl
resource "aws_s3_bucket_object" "upload" {
  bucket = aws_s3_bucket.example_bucket.id
  key =  "photo.jpg"
  source = "/root/photo.jpg"
}
```
An example uploading a file to an S3 bucket

```hcl
resource "aws_key_pair" "web" {
  public_key = "root/.ssh/web.pub"
}

resource "aws_instance" "basic-ec2-server" {
    ami           = "ami-0dc2d3e4c0f9ebd18"
    instance_type = "t2.micro"

    key_name = aws_key_pair.web.id
    tags = {
    #   Name = "Ubuntu"
}
```
An example of using a key pair and referencing it in an EC2 instance

```hcl
data "aws_route53_zone" "primary" {
  name = var.domain-name
}

resource "aws_route53_record" "subdomain" {
  zone_id = data.aws_route53_zone.primary.zone_id
  name    = "${var.subdomain}.${var.domain-name}"
  type    = "A"
  alias {
    name = aws_s3_bucket.web.website_domain
    zone_id = aws_s3_bucket.web.hosted_zone_id
    evaluate_target_health = true
  }
}
```
Setting up an S3 static website Route 53 record

### Syntax - IAM

```hcl
resource "aws_iam_policy" "adminUser" {
  name = "AdminUsers"
  policy = file("admin-policy.json")
}
```
Syntax for creating an IAM policy from a file