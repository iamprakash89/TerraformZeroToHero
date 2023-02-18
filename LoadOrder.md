# Load Order and Semantics

When you are maintaining a large infrastructure it is not possible to keep every configuration in single file.

So, the configuration will be keep in the file as per the requirement.

1) While loading the configurtion, the files will be taken alphabetic order wise.
2) The .tf and .tf.json file only consider to load the configuration other files are ignored.
3) Override file used when you want to create a resource using the same. 


Example:

Directroy contains below files.
```
[root@ip-172-31-24-113 import]# pwd
/opt/import
[root@ip-172-31-24-113 import]# ll
total 28
-rw-r--r-- 1 root root  271 Feb 18 08:38 ec2.tf
-rw-r--r-- 1 root root  272 Feb 18 11:55 override.tf
-rw-r--r-- 1 root root  142 Feb 16 06:52 resource.tf
```

The ```ec2.tf``` file having the below configuration.

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}
```

Now, added the ```override.tf``` file and updated with same resource name. The only change is instance type has been changed.

```
[root@ip-172-31-24-113 import]# cat override.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t3.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}
```

Now we can run the ```terraform plan``` and lets see what configuration is taking.

```
[root@ip-172-31-24-113 import]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
  + resource "aws_instance" "prodserver" {
      + ami                                  = "ami-06e85d4c3149db26a"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      [.....]
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t3.micro"
      + ipv6_address_count                   = (known after apply)
```

The above output shows the override file configuration has been taken in the priority.

Thats it. You did a great job.
