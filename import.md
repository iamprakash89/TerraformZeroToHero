Terraform import command

When you want to move your existing infrastructure to terraform IAC. The Option is ```terraform import```

Also If you are using the terraform already and someuser created a resource in console end without using terraform. 

For those resource as well we can add it on IAC.

The only thing is, it is not going to update/create a configuration file. All the information will be updated in ```terraform state file``` only.


Kindly find the help command which explains what they can do.

```
[root@ip-172-31-24-113 import]# terraform import --help
Usage: terraform [global options] import [options] ADDR ID

  Import existing infrastructure into your Terraform state.

  This will find and import the specified resource into your Terraform
  state, allowing existing infrastructure to come under Terraform
  management without having to be initially created by Terraform.

  The ADDR specified is the address to import the resource to. Please
  see the documentation online for resource addresses. The ID is a
  resource-specific ID to identify that resource being imported. Please
  reference the documentation for the resource type you're importing to
  determine the ID syntax to use. It typically matches directly to the ID
  that the provider uses.

  The current implementation of Terraform import can only import resources
  into the state. It does not generate configuration. A future version of
  Terraform will also generate configuration.

  Because of this, prior to running terraform import it is necessary to write
  a resource configuration block for the resource manually, to which the
  imported object will be attached.

  This command will not modify your infrastructure, but it will make
  network requests to inspect parts of your infrastructure relevant to
  the resource being imported.

Options:

  -config=path            Path to a directory of Terraform configuration files
                          to use to configure the provider. Defaults to pwd.
                          If no config files are present, they must be provided
                          via the input prompts or env vars.

  -input=false            Disable interactive input prompts.

  -lock=false             Don't hold a state lock during the operation. This is
                          dangerous if others might concurrently run commands
                          against the same workspace.

  -lock-timeout=0s        Duration to retry a state lock.

  -no-color               If specified, output won't contain any color.

  -var 'foo=bar'          Set a variable in the Terraform configuration. This
                          flag can be set multiple times. This is only useful
                          with the "-config" flag.

  -var-file=foo           Set variables in the Terraform configuration from
                          a file. If "terraform.tfvars" or any ".auto.tfvars"
                          files are present, they will be automatically loaded.

  -ignore-remote-version  A rare option used for the remote backend only. See
                          the remote backend documentation for more information.

  -state, state-out, and -backup are legacy options supported for the local
  backend only. For more information, see the local backend's documentation.
```

On this directory there is no file till now we crated and there is no state file.

```
[root@ip-172-31-24-113 import]# pwd
/opt/import
[root@ip-172-31-24-113 import]# ll
total 4
-rw-r--r-- 1 root root 131 Feb 14 16:59 resource.tf
[root@ip-172-31-24-113 import]#
```

### Secnario 2
On my AWS console, i have create a ec2 instance and one security group has been attached. Now i need to import those infra into our IAC.

I have gathered the information about previously installed ec2 instance and creating a ```.tf``` file.

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

If i try with ``` terraform plan``` it will show the i item will be crated. However, the instance already created on AWS console end.

```
[root@ip-172-31-24-113 import]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
  + resource "aws_instance" "prodserver" {
[.....]
Plan: 1 to add, 0 to change, 0 to destroy.
```

So, ```Terraform import``` options comes into the picture. When i utilized the option, this will automatically import all the state of the resource.

```
[root@ip-172-31-24-113 import]# terraform import aws_instance.prodserver i-00c27c88a8aee431d
aws_instance.prodserver: Importing from ID "i-00c27c88a8aee431d"...
aws_instance.prodserver: Import prepared!
  Prepared aws_instance for import
aws_instance.prodserver: Refreshing state... [id=i-00c27c88a8aee431d]

Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

- When i enter the terraform plan it shows the in-palce update and there are one more attribute not mentioned in the configuration. due to that the below one is showing as a update;

```
[root@ip-172-31-24-113 import]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-00c27c88a8aee431d]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.prodserver will be updated in-place
  ~ resource "aws_instance" "prodserver" {
        id                                   = "i-00c27c88a8aee431d"
      ~ tags                                 = {
          - "Name" = "prodserver" -> null
        }
      ~ tags_all                             = {
          - "Name" = "prodserver"
        } -> (known after apply)
      + user_data_replace_on_change          = false
        # (30 unchanged attributes hidden)

      - timeouts {}

        # (7 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```

If you use ```terraform apply``` the exisiting instance wont destory IT just update the state. You can add this tags in to the ec2.tf file and resolve the issue.


Try if your self and update in the comments with your updated code.


# scenario 2:  We have created a one more resource which is a security group and wants to add it on exisiting terraform code.

I am taking the pervious code and say, those are in IAC. However, the new sg created in console and wants to add it on IAC.

The new security group name is sg-08d97a26d077796c8.


On our ec2.tf file have added that security group as well.

```
[root@ip-172-31-24-113 import]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                   = "ami-0f1a5f5ada0e7da53"
  instance_type         = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17","sg-08d97a26d077796c8"]
  key_name              = "terraform"
  subnet_id             = "subnet-0ba613b7831428c4d"
}

```

When you use the terraform plan it shows for updating the security group only with in-place update.

```
[root@ip-172-31-24-113 import]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-00c27c88a8aee431d]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  ~ update in-place

Terraform will perform the following actions:

  # aws_instance.prodserver will be updated in-place
  ~ resource "aws_instance" "prodserver" {
        id                                   = "i-00c27c88a8aee431d"
        tags                                 = {}
      ~ vpc_security_group_ids               = [
          + "sg-08d97a26d077796c8",
            # (1 unchanged element hidden)
        ]
        # (31 unchanged attributes hidden)

        # (7 unchanged blocks hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.
```

That's IT. You have done a goog job.
