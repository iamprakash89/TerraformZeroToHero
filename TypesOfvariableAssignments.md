# Variable Assignment Types

#### Approach 1) The first example is for Default variable.

On this directory there are three files available. 

```ec2instance.tf``` is for ec2 instance which contains the ec2 ami and tyep details.
```resource.tf```  file is for resource which contains  the resource provider and authentication details.
```variables.tf``` file  is for variables of what values need to passwed to resource arguments.

```
[root@ip-172-31-24-113 variables]# pwd
/opt/variables
[root@ip-172-31-24-113 variables]# ll
total 12
-rw-r--r-- 1 root root  91 Feb 14 17:01 ec2instance.tf
-rw-r--r-- 1 root root 131 Feb 14 16:59 resource.tf
-rw-r--r-- 1 root root 103 Feb 14 17:04 variables.tf
```

content inside of the files.

```
[root@ip-172-31-24-113 variables]# cat ec2instance.tf
resource "aws_instance" "variabledemo"{
ami = var.amiid
instance_type = var.instancetype
}

[root@ip-172-31-24-113 variables]# cat variables.tf
variable "instancetype"{
default = "t2.micro"
}

variable "amiid"{
default = "ami-06e85d4c3149db26a"
}
```

using the varaible we are passing the values to resource.

Just follow the five steps to check the ec2 creation and destory.

1) ```terraform init``` - download the resource provide modules

2) ```terraform validate```  - validate the syntax error.

3) ```terraform plan```  - at the end of the out put like this, resource not going to create. It is just a plan

```
Plan: 1 to add, 0 to change, 0 to destroy.
```

3) ```terraform apply``` - the command will ask your permission to deploy the resource in the infra.

```
Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes
  
aws_instance.variabledemo: Creating...
aws_instance.variabledemo: Still creating... [10s elapsed]
aws_instance.variabledemo: Still creating... [20s elapsed]
aws_instance.variabledemo: Still creating... [30s elapsed]
aws_instance.variabledemo: Creation complete after 31s [id=i-0d9c390647678e9b7]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

4) ```terraform destroy``` - once testing is completed destory the infra.

```
Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_instance.variabledemo: Destroying... [id=i-0d9c390647678e9b7]
aws_instance.variabledemo: Still destroying... [id=i-0d9c390647678e9b7, 10s elapsed]
aws_instance.variabledemo: Still destroying... [id=i-0d9c390647678e9b7, 20s elapsed]
aws_instance.variabledemo: Still destroying... [id=i-0d9c390647678e9b7, 30s elapsed]
aws_instance.variabledemo: Destruction complete after 30s

Destroy complete! Resources: 1 destroyed.
```



#### Approach 2) Passing a values to arguments using file.

When you are using file you need to create a ```terraform.tfvars```. The file name should be like this only.

Here ``` terraform.tfvars``` file has been create and added the instancetype value.

```
[root@ip-172-31-24-113 variables]# cat terraform.tfvars
instancetype = "t2.nano"
```

on this directory ```variables.tf``` file is also available and those lne contains instance value.

```
[root@ip-172-31-24-113 variables]# cat variables.tf
variable "instancetype"{
default = "t2.micro"
}

variable "amiid"{
default = "ami-06e85d4c3149db26a"
}
```

Both having a different instancetype values. Now i am going to execute the ```terraform plan``` and wants to identify which values is taken.

```
[root@ip-172-31-24-113 variables]# terraform plan | grep -i instance_type
      + instance_type                        = "t3.micro"
```

On the above output instancetype value taken from the files (terraform.tfvars) not from the ```variables.tf```.
##### WHY?. 
This indicates whenever you run the terraform, the values will taken from the order wise(priority). We can talk about this on the end.



#### Approach 3) Passing a values through CLI Flags

Through the command line interface we can pass the values to resource arguments.

running the commands in the same directory which contains the variable,terraform.tfvars files.

I am passing the values throug the cli flag ``` terraform plan -var="instancetype=m4.xlarge"```. Let's see the output.

```
[root@ip-172-31-24-113 variables]# terraform plan -var="instancetype=m4.xlarge" | grep -i instance_type
      + instance_type                        = "m4.xlarge"
```

The value has been taken from the CLI, eventhough we have a defined in the varaibles.tf and .tfvars file.
#### Again WHY?


#### approach 4) passing a values through environment values.


```
[root@ip-172-31-24-113 variables]# export TF_VAR_instancetype=r5.large
[root@ip-172-31-24-113 variables]# echo $TF_VAR_instancetype
r5.large
```


### WHY?

The values will assigned as per the below order
1) Environment Variable
2) CLI Flags
3) From a File
4) Default Variable

This is how the values will be checked from the orderwise. When you run terraform command, values will be checked first at environment varaible.
If there is no environment varaible, then will move to CLI flag, then File then default varaible.
