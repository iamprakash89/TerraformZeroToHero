# Terraform Tainting

Forcing recreation of resources(Tainting)

1) When any changes in the resoruces and want to change to previous state we can use the taint.

2) A resource can be destoryed and recreated.

### How to use tainting
=======================
```
terraform taint resource-id
terraform untaint resource-id
```
EC2 instance already created and going to change configuration in the console end.

The ec2 contains the below configuration. 

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

Changed the instance type from t2.micro to t3.nano.

The below logs shows instance_type has been modified from t2.micro to t3.nano. 

```
[root@ip-172-31-24-113 datasource]# terraform plan
data.aws_ami.web: Reading...
data.aws_ami.web: Read complete after 0s [id=ami-00b44d3dbe1f81742]
aws_instance.prodserver: Refreshing state... [id=i-060f291b319bf6c55]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.prodserver is tainted, so must be replaced
-/+ resource "aws_instance" "prodserver" {
      ~ arn                                  = "arn:aws:ec2:us-west-2:772961762562:instance/i-060f291b319bf6c55" -> (known after apply)
      ~ associate_public_ip_address          = true -> (known after apply)
      ~ availability_zone                    = "us-west-2b" -> (known after apply)
 [.....]
      ~ id                                   = "i-060f291b319bf6c55" -> (known after apply)
      ~ instance_initiated_shutdown_behavior = "stop" -> (known after apply)
      ~ instance_state                       = "running" -> (known after apply)
      ~ instance_type                        = "t3.nano" -> "t2.micro"
  Plan: 1 to add, 0 to change, 1 to destroy.

```

However i do not want to import the state in terraform and want to use the previous state which is in IAC.

On state list shows what are the resoruces created

```
[root@ip-172-31-24-113 datasource]# terraform state list
data.aws_ami.web
aws_instance.prodserver
```

On this ```aws_instance.prodserver``` the configuration has been changed from the console end. Now using tainting utility we can revert back to the previous state.

```
[root@ip-172-31-24-113 datasource]# terraform taint aws_instance.prodserver
Resource instance aws_instance.prodserver has been marked as tainted.
```

After entering to the ```terraform apply``` the tainted resource will be destroyed and recreated.

```
[root@ip-172-31-24-113 datasource]# terraform apply
data.aws_ami.web: Reading...
data.aws_ami.web: Read complete after 0s [id=ami-00b44d3dbe1f81742]
aws_instance.prodserver: Refreshing state... [id=i-060f291b319bf6c55]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.prodserver is tainted, so must be replaced    <============
-/+ resource "aws_instance" "prodserver" {
[.....]

Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.prodserver: Destroying... [id=i-060f291b319bf6c55]
aws_instance.prodserver: Still destroying... [id=i-060f291b319bf6c55, 10s elapsed]
aws_instance.prodserver: Still destroying... [id=i-060f291b319bf6c55, 20s elapsed]
aws_instance.prodserver: Still destroying... [id=i-060f291b319bf6c55, 30s elapsed]
aws_instance.prodserver: Still destroying... [id=i-060f291b319bf6c55, 40s elapsed]
aws_instance.prodserver: Destruction complete after 49s
aws_instance.prodserver: Creating...
aws_instance.prodserver: Still creating... [10s elapsed]
aws_instance.prodserver: Still creating... [20s elapsed]
aws_instance.prodserver: Creation complete after 22s [id=i-0e85602fc896b682a]

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```

using ```terraform untaint resource-id``` you can untaint the same.

Great Acheivement!!!
