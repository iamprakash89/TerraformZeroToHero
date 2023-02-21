# Terraform Provisioner

Provisoner used for executing scripts or shell commands on the server when creation/destruction the resource.

For example, IF you are creating a new EC2 instance through the IAC, However you wan to install some service or some file to copy.

Those works we can do it using a provisoner.

In somecase, If provisoner fails the resource will be tainted.

Types of Provisoners:
1) Creation time provisoner
2) Destory time provisoner

Types of generic provisoner
1) File
2) Local-exec
3) Remote-exec

other provisioners
1) puppet
2) chef
3) saltstak

## Here we talk about file provisoner


On this example, i am going to create a ec2 instance and copying files from local machine using file provisoner.


Note: Before creating a ec2, we need a key pair for this.So create a new key pair on AWS.

I copied the key pair and saved it into terr.pem file.

The below directory contains, ec2 , file which we are going to copy, and key for login the new ec2 instance.
```
[root@terraform provisener]# pwd
/opt/provisener
[root@terraform provisener]# ll
total 32
-rw-r--r-- 1 root root  521 Feb 21 16:15 ec2.tf
-rw-r--r-- 1 root root    5 Feb 21 15:20 provisioner-local-test
-rw-r--r-- 1 root root  142 Feb 16 06:52 resource.tf
-rw-r--r-- 1 root root 4364 Feb 21 16:16 terraform.tfstate
-rw-r--r-- 1 root root 4398 Feb 21 16:15 terraform.tfstate.backup
-r-------- 1 root root 1679 Feb 21 15:28 terr.pem
```

I have added the key name which created for demo purpose. After that provisener i used for file to copy it from local machine to new ec2 instance.
```
[root@terraform provisener]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "terr"
  subnet_id              = "subnet-0ba613b7831428c4d"

provisioner "file" {
  source = "provisioner-local-test"
  destination = "/tmp/provisoner-local-test"
}

connection {
    type     = "ssh"
    host     = self.public_ip
    user     = "ec2-user"
    password = ""
    private_key = file("terr.pem")
}
}
```
The contents available in file.

```
[root@terraform provisener]# cat provisioner-local-test
TEST
```

Once you applied a ``` terraform apply``` command it will create a new instance and copied the files as well.

```
[root@terraform provisener]# cat terraform.tfstate | grep -i ami
            "ami": "ami-06e85d4c3149db26a",
```

EC2 is accessible from terraform server using demo key.

```
[root@terraform provisener]# ssh -i terr.pem ec2-user@34.212.111.73
Last login: Tue Feb 21 16:16:52 2023 from 54.201.122.215

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
16 package(s) needed for security, out of 16 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-31-16-203 ~]$ ll /tmp/
total 4
-rw-r--r-- 1 ec2-user ec2-user  5 Feb 21 16:16 provisoner-local-test
drwx------ 3 root     root     17 Feb 21 16:16 systemd-private-d01e9137d8684544a2a9f1ddb38eca44-chronyd.service-680BQf
```


That's it, You did a great Job.
