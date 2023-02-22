# Terraform Creation-time and Destroy-time Provisoner

There are two different options in provisoner. 

1) creation-time provisoner

   On default without mention anything, when you are creating a resource the creation-time provisoner will execute.
   In case of any failure while executing the terraform plan, the resource will be tainted.
   
   you can check using ```  cat with state file``` or ``` terraform show status``` command.
   
   If you want to overcome with tainted issue you need to add the ```onfailure = continue```
 
2) Destroy-time provisoner

	On the code if you are using ```when =  Destroyed``` then the code will execute only on the resource destoryed time.
  
	In case of any failure while destroying the resource, nothing will happen.

```
[root@terraform creation-destroy-time-provisoner]# pwd
/opt/creation-destroy-time-provisoner

[root@terraform creation-destroy-time-provisoner]# ll
total 16
-r-------- 1 root root 1679 Feb 22 15:51 demo.pem
-rw-r--r-- 1 root root  957 Feb 22 15:51 ec2.tf
-rw-r--r-- 1 root root   54 Feb 22 15:51 index.html-old
-rw-r--r-- 1 root root  142 Feb 22 15:51 resource.tf
```

### creation-time provisoner

On this directory i just moved a file from index.html to old. So, We can look how exactly it works.


```
[root@terraform creation-destroy-time-provisoner]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "demo"
  subnet_id              = "subnet-0ba613b7831428c4d"

  provisioner "file" {
    source      = "index.html"
    destination = "/tmp/index.html"
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }

  provisioner "remote-exec" {
    inline = [
      "sudo yum install httpd -y",
      "sudo systemctl start httpd.service",
      "sudo systemctl enable httpd.service",
      "sudo cp /tmp/index.html /var/www/html/index.html",
    ]

    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }
}
```

Running the ```terraform apply``` command to check the error and tainted status

```
[root@terraform creation-destroy-time-provisoner]# terraform apply

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
  + resource "aws_instance" "prodserver" {
      + ami                                  = "ami-06e85d4c3149db26a"
      + arn                                  = (known after apply)
[.....]
Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.prodserver: Creating...
aws_instance.prodserver: Still creating... [10s elapsed]
aws_instance.prodserver: Still creating... [20s elapsed]
aws_instance.prodserver: Provisioning with 'file'...
╷
│ Error: file provisioner error
│
│   with aws_instance.prodserver,
│   on ec2.tf line 8, in resource "aws_instance" "prodserver":
│    8:   provisioner "file" {
│
│ stat index.html: no such file or directory
╵
```

The ec2 instance created, however the other activities are not completed due to the error.

Now if i check the ```terraform.tfstate``` file from the directory, on manged option we can see the status is tainted.

```
{
  "version": 4,
  "terraform_version": "1.3.8",
  "serial": 1,
  "lineage": "cb36831a-702e-0421-5d07-568d157448d3",
  "outputs": {},
  "resources": [
    {
      "mode": "managed",
      "type": "aws_instance",
      "name": "prodserver",
      "provider": "provider[\"registry.terraform.io/hashicorp/aws\"]",
      "instances": [
        {
          "status": "tainted",
          "schema_version": 1,
          "attributes": {
[.....]
```

Once issue resolved, the resource will be destoryed and recreate the same.

log clearly indicate the same.

```
[root@terraform creation-destroy-time-provisoner]# terraform plan
aws_instance.prodserver: Refreshing state... [id=i-08c618f8d4ef1ce96]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
-/+ destroy and then create replacement

Terraform will perform the following actions:

  # aws_instance.prodserver is tainted, so must be replaced
[.....]
Plan: 1 to add, 0 to change, 1 to destroy.
```

In that case we can untaint the resource or you have to use the ```onfailure=continue```

```
[root@terraform creation-destroy-time-provisoner]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "demo"
  subnet_id              = "subnet-0ba613b7831428c4d"

  provisioner "file" {
    source      = "index.html"
    destination = "/tmp/index.html"
    on_failure = continue
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }

  provisioner "remote-exec" {
    inline = [
      "sudo yum install httpd -y",
      "sudo systemctl start httpd.service",
      "sudo systemctl enable httpd.service",
      "sudo cp /tmp/index.html /var/www/html/index.html",
    ]
   on_failure = continue
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }
}
```

Now again running the ```terraform apply```


```
Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.prodserver: Creating...
aws_instance.prodserver: Still creating... [10s elapsed]
aws_instance.prodserver: Still creating... [20s elapsed]
aws_instance.prodserver: Still creating... [30s elapsed]
aws_instance.prodserver: Provisioning with 'file'...
aws_instance.prodserver: Provisioning with 'remote-exec'...
aws_instance.prodserver (remote-exec): Connecting to remote host via SSH...
aws_instance.prodserver (remote-exec):   Host: 35.164.248.221
aws_instance.prodserver (remote-exec):   User: ec2-user
aws_instance.prodserver (remote-exec):   Password: false
aws_instance.prodserver (remote-exec):   Private key: true
aws_instance.prodserver (remote-exec):   Certificate: false
aws_instance.prodserver (remote-exec):   SSH Agent: false
aws_instance.prodserver (remote-exec):   Checking Host Key: false
aws_instance.prodserver (remote-exec):   Target Platform: unix
aws_instance.prodserver (remote-exec): Connected!
aws_instance.prodserver (remote-exec): Loaded plugins: extras_suggestions,
aws_instance.prodserver (remote-exec):               : langpacks, priorities,
aws_instance.prodserver (remote-exec):               : update-motd
aws_instance.prodserver: Still creating... [40s elapsed]
aws_instance.prodserver (remote-exec): Resolving Dependencies
aws_instance.prodserver (remote-exec): --> Running transaction check
aws_instance.prodserver (remote-exec): ---> Package httpd.x86_64 0:2.4.55-1.amzn2 will be installed
aws_instance.prodserver (remote-exec): --> Processing Dependency: httpd-tools = 2.4.55-1.amzn2 for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: httpd-filesystem = 2.4.55-1.amzn2 for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: system-logos-httpd for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: mod_http2 for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: httpd-filesystem for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: /etc/mime.types for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.55-1.amzn2.x86_64
aws_instance.prodserver (remote-exec): --> Running transaction check
aws_instance.prodserver (remote-exec): ---> Package apr.x86_64 0:1.7.2-1.amzn2 will be installed
aws_instance.prodserver (remote-exec): ---> Package apr-util.x86_64 0:1.6.3-1.amzn2.0.1 will be installed
aws_instance.prodserver (remote-exec): --> Processing Dependency: apr-util-bdb(x86-64) = 1.6.3-1.amzn2.0.1 for package: apr-util-1.6.3-1.amzn2.0.1.x86_64
aws_instance.prodserver (remote-exec): ---> Package generic-logos-httpd.noarch 0:18.0.0-4.amzn2 will be installed
aws_instance.prodserver (remote-exec): ---> Package httpd-filesystem.noarch 0:2.4.55-1.amzn2 will be installed
aws_instance.prodserver (remote-exec): ---> Package httpd-tools.x86_64 0:2.4.55-1.amzn2 will be installed
aws_instance.prodserver (remote-exec): ---> Package mailcap.noarch 0:2.1.41-2.amzn2 will be installed
aws_instance.prodserver (remote-exec): ---> Package mod_http2.x86_64 0:1.15.19-1.amzn2.0.1 will be installed
aws_instance.prodserver (remote-exec): --> Running transaction check
aws_instance.prodserver (remote-exec): ---> Package apr-util-bdb.x86_64 0:1.6.3-1.amzn2.0.1 will be installed
aws_instance.prodserver (remote-exec): --> Finished Dependency Resolution

aws_instance.prodserver (remote-exec): Dependencies Resolved

aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec):  Package      Arch   Version
aws_instance.prodserver (remote-exec):                        Repository  Size
aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec): Installing:
aws_instance.prodserver (remote-exec):  httpd        x86_64 2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core 1.4 M
aws_instance.prodserver (remote-exec): Installing for dependencies:
aws_instance.prodserver (remote-exec):  apr          x86_64 1.7.2-1.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core 130 k
aws_instance.prodserver (remote-exec):  apr-util     x86_64 1.6.3-1.amzn2.0.1
aws_instance.prodserver (remote-exec):                        amzn2-core 101 k
aws_instance.prodserver (remote-exec):  apr-util-bdb x86_64 1.6.3-1.amzn2.0.1
aws_instance.prodserver (remote-exec):                        amzn2-core  22 k
aws_instance.prodserver (remote-exec):  generic-logos-httpd
aws_instance.prodserver (remote-exec):               noarch 18.0.0-4.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core  19 k
aws_instance.prodserver (remote-exec):  httpd-filesystem
aws_instance.prodserver (remote-exec):               noarch 2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core  24 k
aws_instance.prodserver (remote-exec):  httpd-tools  x86_64 2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core  88 k
aws_instance.prodserver (remote-exec):  mailcap      noarch 2.1.41-2.amzn2
aws_instance.prodserver (remote-exec):                        amzn2-core  31 k
aws_instance.prodserver (remote-exec):  mod_http2    x86_64 1.15.19-1.amzn2.0.1
aws_instance.prodserver (remote-exec):                        amzn2-core 149 k

aws_instance.prodserver (remote-exec): Transaction Summary
aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec): Install  1 Package (+8 Dependent packages)

aws_instance.prodserver (remote-exec): Total download size: 1.9 M
aws_instance.prodserver (remote-exec): Installed size: 5.2 M
aws_instance.prodserver (remote-exec): Downloading packages:
aws_instance.prodserver (remote-exec): (1/9): apr-1.7.2-1 | 130 kB   00:00
aws_instance.prodserver (remote-exec): (2/9): apr-util-1. | 101 kB   00:00
aws_instance.prodserver (remote-exec): (3/9): apr-util-bd |  22 kB   00:00
aws_instance.prodserver (remote-exec): (4/9): httpd-2.4.5 | 1.4 MB   00:00
aws_instance.prodserver (remote-exec): (5/9): httpd-files |  24 kB   00:00
aws_instance.prodserver (remote-exec): (6/9): generic-log |  19 kB   00:00
aws_instance.prodserver (remote-exec): (7/9): httpd-tools |  88 kB   00:00
aws_instance.prodserver (remote-exec): (8/9): mailcap-2.1 |  31 kB   00:00
aws_instance.prodserver (remote-exec): (9/9): mod_http2-1 | 149 kB   00:00
aws_instance.prodserver (remote-exec): ----------------------------------------
aws_instance.prodserver (remote-exec): Total      7.5 MB/s | 1.9 MB  00:00
aws_instance.prodserver (remote-exec): Running transaction check
aws_instance.prodserver (remote-exec): Running transaction test
aws_instance.prodserver (remote-exec): Transaction test succeeded
aws_instance.prodserver (remote-exec): Running transaction
aws_instance.prodserver (remote-exec): Installed:
aws_instance.prodserver (remote-exec):   httpd.x86_64 0:2.4.55-1.amzn2
aws_instance.prodserver (remote-exec): Dependency Installed:
aws_instance.prodserver (remote-exec):   apr.x86_64 0:1.7.2-1.amzn2
aws_instance.prodserver (remote-exec):   apr-util.x86_64 0:1.6.3-1.amzn2.0.1
aws_instance.prodserver (remote-exec):   apr-util-bdb.x86_64 0:1.6.3-1.amzn2.0.1
aws_instance.prodserver (remote-exec):   generic-logos-httpd.noarch 0:18.0.0-4.amzn2
aws_instance.prodserver (remote-exec):   httpd-filesystem.noarch 0:2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):   httpd-tools.x86_64 0:2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):   mailcap.noarch 0:2.1.41-2.amzn2
aws_instance.prodserver (remote-exec):   mod_http2.x86_64 0:1.15.19-1.amzn2.0.1
aws_instance.prodserver (remote-exec): Complete!
aws_instance.prodserver (remote-exec): Created symlink from /etc/systemd/system/multi-user.target.wants/httpd.service to /usr/lib/systemd/system/httpd.service.
aws_instance.prodserver (remote-exec): cp: cannot stat ‘/tmp/index.html’: No such file or directory
aws_instance.prodserver: Creation complete after 44s [id=i-05bfc5a0b94fa1fc3]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

On this log, we can clearly able to see the index.html file is not available. However the task has been completed wihtout any error.


### Destroy-time provisoner

Before destorying a server, we need to remove or do some other works. In that case you can use the destroy-time-provisoner.

```
[root@terraform creation-destroy-time-provisoner]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "demo"
  subnet_id              = "subnet-0ba613b7831428c4d"

  provisioner "file" {
    source      = "index.html"
    destination = "/tmp/index.html"
    on_failure = continue
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }

  provisioner "remote-exec" {
    inline = [
      "sudo yum install httpd -y",
      "sudo systemctl start httpd.service",
      "sudo systemctl enable httpd.service",
      "sudo cp /tmp/index.html /var/www/html/index.html",
    ]
   on_failure = continue
    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }

provisioner "remote-exec" {
    when = destroy
    inline = [
      "sudo yum remove httpd -y",
    ]

    connection {
      type        = "ssh"
      host        = self.public_ip
      user        = "ec2-user"
      password    = ""
      private_key = file("demo.pem")
    }

  }

}
```

Now i am destorying the resource and will see what are steps will be coming in the picture.

```
Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_instance.prodserver: Destroying... [id=i-0dcdfe01d3af28640]
aws_instance.prodserver: Provisioning with 'remote-exec'...
aws_instance.prodserver (remote-exec): Connecting to remote host via SSH...
aws_instance.prodserver (remote-exec):   Host: 54.191.43.174
aws_instance.prodserver (remote-exec):   User: ec2-user
aws_instance.prodserver (remote-exec):   Password: false
aws_instance.prodserver (remote-exec):   Private key: true
aws_instance.prodserver (remote-exec):   Certificate: false
aws_instance.prodserver (remote-exec):   SSH Agent: false
aws_instance.prodserver (remote-exec):   Checking Host Key: false
aws_instance.prodserver (remote-exec):   Target Platform: unix
aws_instance.prodserver (remote-exec): Connected!
aws_instance.prodserver (remote-exec): Loaded plugins: extras_suggestions,
aws_instance.prodserver (remote-exec):               : langpacks, priorities,
aws_instance.prodserver (remote-exec):               : update-motd
aws_instance.prodserver (remote-exec): Resolving Dependencies
aws_instance.prodserver (remote-exec): --> Running transaction check
aws_instance.prodserver (remote-exec): ---> Package httpd.x86_64 0:2.4.55-1.amzn2 will be erased
aws_instance.prodserver (remote-exec): --> Processing Dependency: httpd-mmn = 20120211x8664 for package: mod_http2-1.15.19-1.amzn2.0.1.x86_64
aws_instance.prodserver (remote-exec): --> Running transaction check
aws_instance.prodserver (remote-exec): ---> Package mod_http2.x86_64 0:1.15.19-1.amzn2.0.1 will be erased
aws_instance.prodserver (remote-exec): --> Finished Dependency Resolution

aws_instance.prodserver (remote-exec): Dependencies Resolved

aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec):  Package   Arch   Version
aws_instance.prodserver (remote-exec):                       Repository   Size
aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec): Removing:
aws_instance.prodserver (remote-exec):  httpd     x86_64 2.4.55-1.amzn2
aws_instance.prodserver (remote-exec):                       @amzn2-core 4.1 M
aws_instance.prodserver (remote-exec): Removing for dependencies:
aws_instance.prodserver (remote-exec):  mod_http2 x86_64 1.15.19-1.amzn2.0.1
aws_instance.prodserver (remote-exec):                       @amzn2-core 382 k

aws_instance.prodserver (remote-exec): Transaction Summary
aws_instance.prodserver (remote-exec): ========================================
aws_instance.prodserver (remote-exec): Remove  1 Package (+1 Dependent package)

aws_instance.prodserver (remote-exec): Installed size: 4.5 M
aws_instance.prodserver (remote-exec): Downloading packages:
aws_instance.prodserver (remote-exec): Running transaction check
aws_instance.prodserver (remote-exec): Running transaction test
aws_instance.prodserver (remote-exec): Transaction test succeeded
aws_instance.prodserver (remote-exec): Running transaction
aws_instance.prodserver (remote-exec):   Erasing    : httpd-2.4.55-1.amz   1/2
aws_instance.prodserver (remote-exec):   Erasing    : mod_http2-1.15.19-   2/2
aws_instance.prodserver (remote-exec):   Verifying  : mod_http2-1.15.19-   1/2
aws_instance.prodserver (remote-exec):   Verifying  : httpd-2.4.55-1.amz   2/2

aws_instance.prodserver (remote-exec): Removed:
aws_instance.prodserver (remote-exec):   httpd.x86_64 0:2.4.55-1.amzn2

aws_instance.prodserver (remote-exec): Dependency Removed:
aws_instance.prodserver (remote-exec):   mod_http2.x86_64 0:1.15.19-1.amzn2.0.1

aws_instance.prodserver (remote-exec): Complete!
aws_instance.prodserver: Still destroying... [id=i-0dcdfe01d3af28640, 10s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0dcdfe01d3af28640, 20s elapsed]
aws_instance.prodserver: Still destroying... [id=i-0dcdfe01d3af28640, 30s elapsed]
aws_instance.prodserver: Destruction complete after 33s

Destroy complete! Resources: 1 destroyed.
```

The log shows before destory, httpd package has been removed and instance are destroyed.

You did it.. Great!!!
