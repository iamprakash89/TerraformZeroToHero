# Datatypes in Variables

### There are Five types available.
1) String
2) Number
3) List
4) Bool
5) map

Using these, we are assinging the values to variables.

### creating a IAMuser and assigning a string and number values as per our requirement.

The directory contains the below ```.tf``` files. 

```
[root@ip-172-31-24-113 datatype]# ll
total 12
-rw-r--r-- 1 root root  59 Feb 15 05:52 iamusercreation.tf
-rw-r--r-- 1 root root 131 Feb 14 16:59 resource.tf
-rw-r--r-- 1 root root  40 Feb 15 05:54 variables.tf
```
On below code does not contain any datatype. So, the default values will be taken and it's a string. While providing any values it will be taken as a string.

```
[root@ip-172-31-24-113 datatype]# cat iamusercreation.tf
resource "aws_iam_user" "datatype"{
  name = var.iamuser
}
[root@ip-172-31-24-113 datatype]# cat variables.tf
variable "iamuser" {
}
```

As per the below input, while executing the ```terraform plan``` it  requires a input from the user and get the values. Because we havent set any value in variables.

```
[root@ip-172-31-24-113 datatype]# terraform plan
var.iamuser
  Enter a value: string   <==== string value accepted


Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.datatype will be created
  + resource "aws_iam_user" "datatype" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "string"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```
Even number also taken because the default values are string only.

```
[root@ip-172-31-24-113 datatype]# terraform plan
var.iamuser
  Enter a value: 12345  <==== by default everything is string.

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.datatype will be created
  + resource "aws_iam_user" "datatype" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "12345"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```


### Changing the datatype variable from string to number.

On ```variables.tf``` contains type and the value is number and executing the ``` terraform plan" 

```
[root@ip-172-31-24-113 datatype]# cat variables.tf
variable "iamuser" {
        type = number
}
```

When entering the string value it gives error, because datype is now number.

```
[root@ip-172-31-24-113 datatype]# terraform plan
var.iamuser
  Enter a value: string

╷
│ Error: Invalid value for input variable
│
│   on variables.tf line 1:
│    1: variable "iamuser" {
│
│ Unsuitable value for var.iamuser set using an interactive prompt: a number is required.
╵
```

when providing the number values it takes and provided the plan details.

```
[root@ip-172-31-24-113 datatype]# terraform plan
var.iamuser
  Enter a value: 12345


Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_iam_user.datatype will be created
  + resource "aws_iam_user" "datatype" {
      + arn           = (known after apply)
      + force_destroy = false
      + id            = (known after apply)
      + name          = "12345"
      + path          = "/"
      + tags_all      = (known after apply)
      + unique_id     = (known after apply)
    }

Plan: 1 to add, 0 to change, 0 to destroy.
```

Some examples we can use using the below for other datatype variables.

https://registry.terraform.io/modules/terraform-aws-modules/elb/aws/latest
