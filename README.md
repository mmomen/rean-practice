# REAN Assessement

## Objective:

Use CM tools such as Puppet, Ansible, or Chef to automate the installation of basic Drupal or WordPress. Setup a sample site. Automate the solution using Cloudformation template. 

## Deliverable:

A cloudformation template that accepts user inputs as parameters where applicable ( for example, Admin password). This template should setup VPC, create subnets, launch a CM instance, pull the necessary code (modules, classes, recipes etc) from a GIT repo (or S3), and configure the web instance for basic Drupal or Wordpress setup.

## Documentation

### Setup, Resources

* cfn.yaml
    * The Cloudformation template that sets up the infrastructure.
    * The resources include:
        * VPC (mm-vpc)
            * Security Groups
                * SSHSecurityGroup
                    * Port 22 open to IP specified in `SSHIP` parameter
                * WebDMZSecurityGroup
                    * Ports 80 and 443 open to CIDR block specified in `WebDMZAccess` parameter
            * Subnets
                * Public Subnet
                    * Instances launched in this subnet will receive a public IP on launch by default
                * Private Subnet
            * IGW
            * NAT Gateway
            * Route Tables
                * Public Route Table, associated with Public Subnet
                    * `0.0.0.0/0` route to IGW
                * Private Route Table, associate with Private Subnet
                    * `0.0.0.0/0` route to NAT Gateway
            * EC2
                * Chef Instance (Name: Chef 12)
                    * AMI: Amazon Linux 2017.09.1 (currently hardcoded AMI for us-east-1)
                    * Instance type: t2.micro (hardcoded currently)
                    * Subnet: private
                    * Security group(s): WebDMZSecurityGroup

### Parameters

* `VPCName`: String, name of the VPC
* `KeyName`: Name of an existing EC2 KeyPair to enable SSH access to the instance
* `DatabaseName`: String
* `DatabaseUser`: String
* `DatabasePassword`: String, no default, `NoEcho` enabled
* `SSHIP`: String, CIDR block to allow SSH (e.g. `123.123.123.123/32`) within `SSHSecurityGroup`
* `WebDMZAccess`: String, CIDR block to allow HTTP/HTTPS within `WebDMZSecurityGroup`

### Mappings
```yaml
Mappings:
  SubnetConfig:
    VPC:
      CIDR: 172.20.0.0/16
    Public:
      CIDR: 172.20.0.0/24
    Private:
      CIDR: 172.20.1.0/24
```

## TODO

### Working on:

CFN Template:
- Use init to install packages on Chef box
- Configure Chef
- Create iam role and update ec2 instance to use (*if needed)

Chef:
- AWS module
- Launch EC2 instance (Wordpress box)
- Configure Wordpress

### Improvements:
- storage, s3 instead of local on wordpress box
    - Use Chef to apply IAM role to WP box
- move db off onto an rds instance
- make cidrs to parameters
- make vpc name a parameter
- make CIDR blocks available as a parameter
- add descriptions for parameters

### Completed:

CFN Template:
- db parameters
- vpc
- ssh sg
- pub subnet
- priv subnet
- igw
- attach igw
- public route table
- public route for igw
- public route table association
- elastic IP
- nat gateway
- private route table
- web-dmz sg
- ec2 instance for chef
    - ami
    -instance type
    - subnet
    - sg
    -key
    -tag/name
