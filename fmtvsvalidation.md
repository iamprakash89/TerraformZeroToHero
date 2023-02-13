# fmt and validate difference.

Let's take a example of ec2 creation .tf file here.


Intentionally i have rearranged the main.tf file. as you can see the format is changed.

### example 1

```
[root@ip-172-31-24-113 output]# cat main.tf
resource "aws_instance" "terraformdemo"        {
  ami  = "ami-06e85d4c3149db26a"
           instance_type = "t2.micro"
         }
```

I am going to use the terraform fmt and validate command and will see the result.

For ```terraform validate``` command checked the syantx only. In this case there is no syntax error, it throws all are fine.

```
[root@ip-172-31-24-113 output]# terraform validate
Success! The configuration is valid.
```

For ```terraform fmt``` command, It shows the ```main.tf``` file. After reviewing it, the format has been structured.

```
[root@ip-172-31-24-113 output]# terraform fmt
main.tf
[root@ip-172-31-24-113 output]# cat main.tf
resource "aws_instance" "terraformdemo" {
  ami           = "ami-06e85d4c3149db26a"
  instance_type = "t2.micro"
}
```


### example 2

modified the arguments name from ```ami``` to ```amiq```. 


fmt command shows there is some error in the main.tf file. However, it is not providing much information about where the error coming.
```
[root@ip-172-31-24-113 output]# terraform fmt
main.tf
```
validate command provided exactly what is the error and what terraform are looking for.All information provided in the error logs.
```
[root@ip-172-31-24-113 output]# terraform validate
╷
│ Error: Unsupported argument
│
│   on main.tf line 2, in resource "aws_instance" "terraformdemo":
│    2:   amiq          = "ami-06e85d4c3149db26a"
│
│ An argument named "amiq" is not expected here. Did you mean "ami"?
╵
```

### Validation check the syntax and identied any syntax issue there or not and display with brief logs.
### format     check the alignment and structured the codes. also indicates issues are in specific file but not with brief details.  
