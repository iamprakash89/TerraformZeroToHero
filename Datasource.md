# Terraform DataSource

It allows to use information from outside terraform. For example, When i want to create a ec2 instance we have to provide a ami id. 

In our case using ```terraform datasource```, we are going to gather the ami-id from aws resource provider.

The below code shows how we manually pass the ami.

```
[root@ip-172-31-24-113 datasource]# pwd
/opt/datasource

[root@ip-172-31-24-113 datasource]# ll
total 8
-rw-r--r-- 1 root root 271 Feb 18 08:38 ec2.tf
-rw-r--r-- 1 root root 142 Feb 16 06:52 resource.tf

[root@ip-172-31-24-113 datasource]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}
```

Now we can see, how to get the current ami-id from the AWS.

The below code shows instead of providing a hardcode ami, i have moved to external datasource.

```
[root@ip-172-31-24-113 datasource]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = data.aws_ami.web.id
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}

data "aws_ami" "web" {
  most_recent = true
  owners = ["amazon"]

  filter {
    name   = "name"
    values = ["amzn2-ami-hvm*"]
  }
}
```

When executing a ```terraform paln``` you will get a recent amazon ami id where the region you updated in the provider.

```
[root@ip-172-31-24-113 datasource]# terraform plan
data.aws_ami.web: Reading...
data.aws_ami.web: Read complete after 0s [id=ami-00b44d3dbe1f81742]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
  + resource "aws_instance" "prodserver" {
      + ami                                  = "ami-00b44d3dbe1f81742"
      + arn                                  = (known after apply)
[.....]
Plan: 1 to add, 0 to change, 0 to destroy.
```

Great, you learned it. Keep it up.
