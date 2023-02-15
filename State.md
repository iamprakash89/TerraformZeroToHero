# Terraform State Command with help

```
[root@ip-172-31-24-113 opt]# terraform state --help
Usage: terraform [global options] state <subcommand> [options] [args]

  This command has subcommands for advanced state management.

  These subcommands can be used to slice and dice the Terraform state.
  This is sometimes necessary in advanced cases. For your safety, all
  state management commands that modify the state create a timestamped
  backup of the state prior to making modifications.

  The structure and output of the commands is specifically tailored to work
  well with the common Unix utilities such as grep, awk, etc. We recommend
  using those tools to perform more advanced state tasks.

Subcommands:
    list                List resources in the state
    mv                  Move an item in the state
    pull                Pull current state and output to stdout
    push                Update remote state from a local state file
    replace-provider    Replace provider in the state
    rm                  Remove instances from the state
    show                Show a resource in the state
```

Example Directory

```
[root@ip-172-31-24-113 state]# pwd
/opt/state
[root@ip-172-31-24-113 state]# ll
total 12
-rw-r--r-- 1 root root  59 Feb 15 05:52 iamusercreation.tf
-rw-r--r-- 1 root root 131 Feb 14 16:59 resource.tf
-rw-r--r-- 1 root root  39 Feb 15 06:30 variables.tf
-rw-r--r-- 1 root root   20 Feb 15 12:58 terraform.tfvars
```

File ```iamusercreation.tf``` used to create a two resoucres ec2 and iam user.

```
[root@ip-172-31-24-113 state]# cat iamusercreation.tf
resource "aws_iam_user" "username"{
  name = var.iamuser
}

resource "aws_instance" "state"{
  ami = "ami-06e85d4c3149db26a"
  instance_type = "t2.micro"
}

output "iam_username"{
  value = aws_iam_user.username.name
}
```

File ```variables.tf``` contains iamuser

```
[root@ip-172-31-24-113 state]# cat variables.tf
variable "iamuser"{
}
```

Passing the values to attributes .

```
[root@ip-172-31-24-113 state]# cat terraform.tfvars
iamuser="devopskvk"
```

verify the syntax check

```
[root@ip-172-31-24-113 state]# terraform validate
Success! The configuration is valid.
```

Before applying, when entering the ```terraform state list``` which display nothing. ### WHY?

```
[root@ip-172-31-24-113 state]# terraform state list
[root@ip-172-31-24-113 state]#
```
### Answer for WHY?
Because List command will dispaly only the resource state. However, we havent created the resources yet.
Due to that there is state on the about output.

Now verify the output with plan what exactly is going to happen in infra ```terraform plan```.

```
[root@ip-172-31-24-113 state]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.username will be created
  + resource "aws_iam_user" "username" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "devopskvk"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

  # aws_instance.state will be created
  + resource "aws_instance" "state" {
      + ami                                  = "ami-06e85d4c3149db26a"
      + arn                                  = (known after apply)
      + associate_public_ip_address          = (known after apply)
      + availability_zone                    = (known after apply)
      + cpu_core_count                       = (known after apply)
      + cpu_threads_per_core                 = (known after apply)
      + disable_api_stop                     = (known after apply)
      + disable_api_termination              = (known after apply)
      + ebs_optimized                        = (known after apply)
      + get_password_data                    = false
      + host_id                              = (known after apply)
      + host_resource_group_arn              = (known after apply)
      + iam_instance_profile                 = (known after apply)
      + id                                   = (known after apply)
      + instance_initiated_shutdown_behavior = (known after apply)
      + instance_state                       = (known after apply)
      + instance_type                        = "t2.micro"
      + ipv6_address_count                   = (known after apply)
      + ipv6_addresses                       = (known after apply)
      + key_name                             = (known after apply)
      + monitoring                           = (known after apply)
      + outpost_arn                          = (known after apply)
      + password_data                        = (known after apply)
      + placement_group                      = (known after apply)
      + placement_partition_number           = (known after apply)
      + primary_network_interface_id         = (known after apply)
      + private_dns                          = (known after apply)
      + private_ip                           = (known after apply)
      + public_dns                           = (known after apply)
      + public_ip                            = (known after apply)
      + secondary_private_ips                = (known after apply)
      + security_groups                      = (known after apply)
      + source_dest_check                    = true
      + subnet_id                            = (known after apply)
      + tags_all                             = (known after apply)
      + tenancy                              = (known after apply)
      + user_data                            = (known after apply)
      + user_data_base64                     = (known after apply)
      + user_data_replace_on_change          = false
      + vpc_security_group_ids               = (known after apply)

      + capacity_reservation_specification {
          + capacity_reservation_preference = (known after apply)

          + capacity_reservation_target {
              + capacity_reservation_id                 = (known after apply)
              + capacity_reservation_resource_group_arn = (known after apply)
            }
        }

      + ebs_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + snapshot_id           = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }

      + enclave_options {
          + enabled = (known after apply)
        }

      + ephemeral_block_device {
          + device_name  = (known after apply)
          + no_device    = (known after apply)
          + virtual_name = (known after apply)
        }

      + maintenance_options {
          + auto_recovery = (known after apply)
        }

      + metadata_options {
          + http_endpoint               = (known after apply)
          + http_put_response_hop_limit = (known after apply)
          + http_tokens                 = (known after apply)
          + instance_metadata_tags      = (known after apply)
        }

      + network_interface {
          + delete_on_termination = (known after apply)
          + device_index          = (known after apply)
          + network_card_index    = (known after apply)
          + network_interface_id  = (known after apply)
        }

      + private_dns_name_options {
          + enable_resource_name_dns_a_record    = (known after apply)
          + enable_resource_name_dns_aaaa_record = (known after apply)
          + hostname_type                        = (known after apply)
        }

      + root_block_device {
          + delete_on_termination = (known after apply)
          + device_name           = (known after apply)
          + encrypted             = (known after apply)
          + iops                  = (known after apply)
          + kms_key_id            = (known after apply)
          + tags                  = (known after apply)
          + throughput            = (known after apply)
          + volume_id             = (known after apply)
          + volume_size           = (known after apply)
          + volume_type           = (known after apply)
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + iam_username = "devopskvk"
```
As per the log, we are seeing ec2 instance and iamuser is going to create.


using ```terraform apply``` creating the two resoucres.

```
Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + iam_username = "devopskvk"

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.state: Creating...
aws_iam_user.username: Creating...
aws_iam_user.username: Creation complete after 0s [id=devopskvk]
aws_instance.state: Still creating... [10s elapsed]
aws_instance.state: Still creating... [20s elapsed]
aws_instance.state: Still creating... [30s elapsed]
aws_instance.state: Creation complete after 31s [id=i-0867940482e78c2a3]

Apply complete! Resources: 2 added, 0 changed, 0 destroyed.

Outputs:

iam_username = "devopskvk"
```

###  ```terraform state list``` command.
```
[root@ip-172-31-24-113 state]# terraform state list
aws_iam_user.username
aws_instance.state
```

```Terraform mv``` command used to change the current state of the resource.

### moving the resource state 

From the file we have changed the username to devname.

```
[root@ip-172-31-24-113 state]# cat iamusercreation.tf
resource "aws_iam_user" "devname"{
  name = var.iamuser
}

resource "aws_instance" "state"{
  ami = "ami-06e85d4c3149db26a"
  instance_type = "t2.micro"
}

output "iam_username"{
  value = aws_iam_user.devname.name
}
```

### Terraform state MV command

```
[root@ip-172-31-24-113 state]# terraform state mv aws_iam_user.username aws_iam_user.devname
Move "aws_iam_user.username" to "aws_iam_user.devname"
Successfully moved 1 object(s).
```

Now it shows, there is no change in the infra.

```
[root@ip-172-31-24-113 state]# terraform plan
aws_instance.state: Refreshing state... [id=i-0867940482e78c2a3]
aws_iam_user.devname: Refreshing state... [id=devopskvk]
```

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

```
[root@ip-172-31-24-113 state]# terraform plan
aws_instance.state: Refreshing state... [id=i-0867940482e78c2a3]
aws_iam_user.devname: Refreshing state... [id=devopskvk]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.
[root@ip-172-31-24-113 state]# terraform apply
aws_instance.state: Refreshing state... [id=i-0867940482e78c2a3]
aws_iam_user.devname: Refreshing state... [id=devopskvk]

No changes. Your infrastructure matches the configuration.

Terraform has compared your real infrastructure against your configuration and found no differences, so no changes are needed.

Apply complete! Resources: 0 added, 0 changed, 0 destroyed.

Outputs:

iam_username = "devopskvk"
```

After the chagnes, when i am entering the ```terraform state list``` it shows the aws_iam_user.devname from iam_user.username.

```
[root@ip-172-31-24-113 state]# terraform state list
aws_iam_user.devname
aws_instance.state
```
That's how without touching the resource just modifying the state.

Great, Learned about MV command


### Terraform state pull command used for taking a backup into localhost.

using the below command you can take a backup of current state of the resource.
```
[root@ip-172-31-24-113 state]# terraform state pull > backupname
[root@ip-172-31-24-113 state]#
```

In somecase, you don't need a changes in any of the changes and wants to exclude from this.
You can you the terraform state rm command to exclude those.

As per our previous state we have two resource state details available.

```
[root@ip-172-31-24-113 state]# terraform state list
aws_iam_user.devname
aws_instance.state
```

### Terraform state rm

No i want to remove one state from the list. For that we can use the below command

```
[root@ip-172-31-24-113 state]# terraform state rm aws_iam_user.devname
```

It will remove the specific state.


GREAT JOB!!
