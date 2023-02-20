# Terraform Splat Expression

To get all list of attributes.

For example, when you are creating a single instance it is easy to retrive the attributes.

The directory contains the below files.
```
[root@terraform splat]# pwd
/opt/splat
[root@terraform splat]# ll
total 8
-rw-r--r-- 1 root root 354 Feb 20 15:41 ec2.tf
-rw-r--r-- 1 root root 142 Feb 16 06:52 resource.tf
```

The below configuration shows, creating a ec2 instance and retriving a private ip address using output.

```
[root@terraform splat]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "terraform"
  subnet_id              = "subnet-0ba613b7831428c4d"
}

output "instance_ip_address" {
  value = aws_instance.prodserver.private_ip
}
```

When using the ```terraform plan``` it display the private ip details.

```
[root@terraform splat]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
  + resource "aws_instance" "prodserver" {
      + ami                                  = "ami-06e85d4c3149db26a"

[.....]
Plan: 1 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_ip_address = (known after apply)
```


Added counts for how many ec2 instances you need.

```
[root@terraform splat]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "terraform"
  subnet_id              = "subnet-0ba613b7831428c4d"
  count = 3
}

output "instance_ip_address" {
  value = aws_instance.prodserver.private_ip
}
```

Now ```terraform plan``` got a error, becuase of the index not set.
```
[root@terraform splat]# terraform plan
╷
│ Error: Missing resource instance key
│
│   on ec2.tf line 11, in output "instance_ip_address":
│   11:   value = aws_instance.prodserver.private_ip
│
│ Because aws_instance.prodserver has "count" set, its attributes must be accessed on specific instances.
│
│ For example, to correlate with indices of a referring resource, use:
│     aws_instance.prodserver[count.index]
╵
```

Added the splat expression to gather the list of attributes.

```
[root@terraform splat]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "terraform"
  subnet_id              = "subnet-0ba613b7831428c4d"
  count = 3
}

output "instance_ip_address" {
  value = aws_instance.prodserver[*].private_ip
}
```

Now ```terraform plan``` gathered a total instance count.

```
Plan: 3 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + instance_ip_address = [
      + (known after apply),
      + (known after apply),
      + (known after apply),
    ]
```

That's it. Great you learned it.

[.....] - Skipping some lines to reduce the total number of line.
