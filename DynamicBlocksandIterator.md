# Dynamic Blocks and Iterator

For enabling a ingress and egress rules to four ports takes near by 50 lines.
On production environment it is not possible to for keeping multiples lines wehn you want to add multiple ports.

```
[root@ip-172-31-24-113 dynamic]# pwd
/opt/dynamic
[root@ip-172-31-24-113 dynamic]# ll
total 8
-rw-r--r-- 1 root root 142 Feb 16 06:52 resource.tf
-rw-r--r-- 1 root root 974 Feb 18 18:07 sgcreation.tf
```

```
[root@ip-172-31-24-113 dynamic]# cat sgcreation.tf
resource "aws_security_group" "devopskvk" {
  name        = "allow_tls"
  description = "Allow TLS inbound traffic"
  vpc_id      = aws_vpc.main.id

  ingress {
    from_port        = 443
    to_port          = 443
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 443
    to_port          = 443
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  ingress {
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 80
    to_port          = 80
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  ingress {
    from_port        = 8080
    to_port          = 8080
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

  egress {
    from_port        = 8080
    to_port          = 8080
    protocol         = "tcp"
    cidr_blocks      = ["0.0.0.0/0"]
  }

}
```

In-order to reduce the confusion, utility dynamic blocks are used and values are passing through the ```variables.tf``` file.

The below outout shows we have added all the ports in one line and passing this values to ```sgcreation.tf``` file

```
[root@ip-172-31-24-113 dynamic]# cat variables.tf
variable "sg_ports" {

        type = list(number)
        description = "list of ingress egress ports"
        default = [9001,443,80,8080,9090]
}
```

Simple sg_creation contains with dynamic block.

```
[root@ip-172-31-24-113 dynamic]# cat sg_creation.tf
resource "aws_security_group" "devopskvk" {
  name        = "allow_tls"
  description = "Allow TLS inbound traffic"


dynamic "ingress" {
    for_each = var.sg_ports
    content {
      from_port        =  ingress.value
      to_port          =  ingress.value
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
    }
  }


dynamic "egress" {
    for_each = var.sg_ports
    content {
      from_port        =  egress.value
      to_port          =  egress.value
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
    }
  }

}
```

When i use ```terraform plan``` it passed the values to all the ports.

```
[root@ip-172-31-24-113 dynamic]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_security_group.devopskvk will be created
  + resource "aws_security_group" "devopskvk" {
      + arn                    = (known after apply)
      + description            = "Allow TLS inbound traffic"
      + egress                 = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 443
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 443
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 8080
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 8080
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 80
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 80
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 9001
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 9001
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 9090
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 9090
            },
        ]
      + id                     = (known after apply)
      + ingress                = [
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 443
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 443
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 8080
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 8080
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 80
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 80
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 9001
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 9001
            },
          + {
              + cidr_blocks      = [
                  + "0.0.0.0/0",
                ]
              + description      = ""
              + from_port        = 9090
              + ipv6_cidr_blocks = []
              + prefix_list_ids  = []
              + protocol         = "tcp"
              + security_groups  = []
              + self             = false
              + to_port          = 9090
            },
        ]
      + name                   = "allow_tls"
      + name_prefix            = (known after apply)
      + owner_id               = (known after apply)
      + revoke_rules_on_delete = false
      + tags_all               = (known after apply)
      + vpc_id                 = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.

```

Adding a ```iterator``` is one of the advantage when we are passing a values through the dynamic blocks.
On those blocks instead of using the dynamic name, we can use our termproary iterator name.

So this will help us to simplify what values we are passing.

now directory contians below files.

```
[root@ip-172-31-24-113 dynamic]# pwd
/opt/dynamic
[root@ip-172-31-24-113 dynamic]# ll
total 16
-rw-r--r-- 1 root root 142 Feb 16 06:52 resource.tf
-rw-r--r-- 1 root root 578 Feb 18 18:27 sg_creation.tf
-rw-r--r-- 1 root root 974 Feb 18 18:07 sgcreation.tf-bak
-rw-r--r-- 1 root root 127 Feb 18 18:19 variables.tf
```

On the below logs iterator has been added instead of using ```ingress.value & egress.value``` we changes to ```port.value```. 

```
[root@ip-172-31-24-113 dynamic]# cat sg_creation.tf
resource "aws_security_group" "devopskvk" {
  name        = "allow_tls"
  description = "Allow TLS inbound traffic"


dynamic "ingress" {
    for_each = var.sg_ports
    iterator = port
    content {
      from_port        =  port.value
      to_port          =  port.value
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
    }
  }


dynamic "egress" {
    for_each = var.sg_ports
    content {
      from_port        =  port.value
      to_port          =  port.value
      protocol         = "tcp"
      cidr_blocks      = ["0.0.0.0/0"]
    }
  }

}

```

That's it. You did a great Job.
