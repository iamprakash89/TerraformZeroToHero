# TerraForm LifeCycle

Sometimes we do not want to delete or update the resources which is already created. example, if any production services are running and when update happens,  terraform will delete the old infra and create a new infra. Due to that the production application will be down for some mins/hours.

So, It is important to prevent these kind of state. For that we are applying the ```terraform lifecyle```.

What exactly we can do with this?

When we use the terraform lifecyle policy, The exisiting environment will not be deleted until the new environment created.
It makes the application availability.

There are some arguments we can use.
1) Create_before_destroy (bool)
2) prevent_destory  (bool)
3) ignore_change (list of attribute name)

You have to add the below lines for lifecyle:

```
lifecycle{
  lifecycle_argument = bool
}
```

On this example, I am taking a ec2 instance service.

Resource EC2 instance is already created and the state is available in the directory.

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-0f1a5f5ada0e7da53"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}
```

```
[root@ip-172-31-24-113 import]# terraform state list
aws_instance.prodserver
```

Before using the ```lifecycle``` policy, I just changed the ami for new instance.

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

The ```terraform plan``` output shows destroy and create replacement of the respective resource.

```
[root@ip-172-31-24-113 import]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-0d2df3b0cce655d7b]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.prodserver must be replaced
-/+ resource "aws_instance" "prodserver" {
      ~ ami                                  = "ami-0f1a5f5ada0e7da53" -> "ami-06e85d4c3149db26a" # forces replacement
      ~ arn                                  = "arn:aws:ec2:us-west-2:772961762562:instance/i-0d2df3b0cce655d7b" -> (known after apply)
      ~ associate_public_ip_address          = true -> (known after apply)
```

On this EC2, Production application is running and do not want any downtime. i applied a lifecycle policy here.

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"

lifecyle{
  create_before_destroy = true
}

}
```

After changing the terraform lifecycle policy as per the plan, ec2 instance will be created first and then destoryed the older one.

```
[root@ip-172-31-24-113 import]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-0d2df3b0cce655d7b]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
+/- create replacement and then destroy
```


After that using terraform apply command the instance will be created first and destory the older instance.

```
[root@ip-172-31-24-113 import]# terraform apply
aws_instance.prodserver: Refreshing state... [id=i-0d2df3b0cce655d7b]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
+/- create replacement and then destroy

Terraform will perform the following actions:

  # aws_instance.prodserver must be replaced
+/- resource "aws_instance" "prodserver" {
      ~ ami                                  = "ami-0f1a5f5ada0e7da53" -> "ami-06e85d4c3149db26a" # forces replacement
      ~ arn                                  = "arn:aws:ec2:us-west-2:772961762562:instance/i-0d2df3b0cce655d7b" -> (known after apply)
      ~ associate_public_ip_address          = true -> (known after apply)
[.....]
Plan: 1 to add, 0 to change, 1 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.prodserver: Creating...
aws_instance.prodserver: Still creating... [10s elapsed]
aws_instance.prodserver: Still creating... [20s elapsed]
        aws_instance.prodserver: Still creating... [30s elapsed]
aws_instance.prodserver: Creation complete after 31s [id=i-014016f05a3a910d3]
aws_instance.prodserver (deposed object 8c02f1e5): Destroying... [id=i-0d2df3b0cce655d7b]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 10s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 20s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 30s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 40s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 50s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m0s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m10s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m20s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m30s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m40s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 1m50s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 2m0s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 2m10s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0d2df3b0cce655d7b, 2m20s elapsed]
aws_instance.prodserver: Destruction complete after 2m20s

Apply complete! Resources: 1 added, 0 changed, 1 destroyed.
```

## Try
use other two lifecycle policy and update in the comments.

That's it, You did a great Job.
