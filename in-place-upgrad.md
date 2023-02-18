# In-Place-Upgrade 

When you are changing the resource arguments, sometimes it is not necessary to destroy and recreate a resource.

### Scenario:

I have created a EC2 instance and changing the arguments.

Before:

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"


lifecycle {
  create_before_destroy = true
}
}

```

Changed the ```instance_type``` to check whether this is going to destory and recreating the instance or not.

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t3.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"


lifecycle {
  create_before_destroy = true
}
}
```

The below output shows the resource is going to update the configurtion in-place only.

Not destroying first and recreation.

```
[root@ip-172-31-24-113 import]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-08ea5d3cbcab41d59]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.prodserver will be updated in-place
  ~ resource "aws_instance" "prodserver" {
        id                                   = "i-08ea5d3cbcab41d59"
      ~ instance_type                        = "t2.micro" -> "t3.micro"
        tags                                 = {}
        # (31 unchanged attributes hidden)

        # (7 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```

Great, You have done a good job.
