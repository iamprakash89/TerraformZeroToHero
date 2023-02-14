# Variable Assignment Types

#### The first example is for Default variable.

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

3) ```terrafom apply``` - the command will ask your permission to deploy the resource in the infra.

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
###### Task 1 is completed 
