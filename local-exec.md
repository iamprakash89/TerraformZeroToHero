# local-exec provisioner

``` local-exec``` provisioner is used to execute a command on the machine running Terraform, rather than on the instance being created.
 This can be useful for performing tasks such as initializing a database or installing software on the machine running Terraform.
 
On this example, we are creating a AWS EC2 instance and intalling a httpd packges on the instance thorugh the terraform server.

```
[root@terraform local-exec]# cat ec2.tf
resource "aws_instance" "prodserver" {
  ami                    = "ami-06e85d4c3149db26a"
  instance_type          = "t2.micro"
  vpc_security_group_ids = ["sg-005faf960f0abce17"]
  key_name               = "demo"
  subnet_id              = "subnet-0ba613b7831428c4d"

provisioner "local-exec" {
        command = "sudo yum install httpd -y"
}
```

When applying the ```terraform apply``` command the below logs shows ec2 instance has been created and httpd service installed from the ```local-exec```

```
Now the local-exec to install the latest packages on the server.

Plan: 1 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

aws_instance.prodserver: Creating...
aws_instance.prodserver: Still creating... [10s elapsed]
aws_instance.prodserver: Still creating... [20s elapsed]
aws_instance.prodserver: Still creating... [30s elapsed]
aws_instance.prodserver: Provisioning with 'local-exec'...
aws_instance.prodserver (local-exec): Executing: ["/bin/sh" "-c" "yum install httpd -y"]
aws_instance.prodserver (local-exec): Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
aws_instance.prodserver (local-exec): Resolving Dependencies
aws_instance.prodserver (local-exec): --> Running transaction check
aws_instance.prodserver (local-exec): ---> Package httpd.x86_64 0:2.4.54-1.amzn2 will be installed
aws_instance.prodserver (local-exec): --> Processing Dependency: httpd-tools = 2.4.54-1.amzn2 for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: httpd-filesystem = 2.4.54-1.amzn2 for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: system-logos-httpd for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: mod_http2 for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: httpd-filesystem for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: /etc/mime.types for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.54-1.amzn2.x86_64
aws_instance.prodserver (local-exec): --> Running transaction check
aws_instance.prodserver (local-exec): ---> Package apr.x86_64 0:1.7.2-1.amzn2 will be installed
aws_instance.prodserver (local-exec): ---> Package apr-util.x86_64 0:1.6.3-1.amzn2.0.1 will be installed
aws_instance.prodserver (local-exec): --> Processing Dependency: apr-util-bdb(x86-64) = 1.6.3-1.amzn2.0.1 for package: apr-util-1.6.3-1.amzn2.0.1.x86_64
aws_instance.prodserver (local-exec): ---> Package generic-logos-httpd.noarch 0:18.0.0-4.amzn2 will be installed
aws_instance.prodserver (local-exec): ---> Package httpd-filesystem.noarch 0:2.4.54-1.amzn2 will be installed
aws_instance.prodserver (local-exec): ---> Package httpd-tools.x86_64 0:2.4.54-1.amzn2 will be installed
aws_instance.prodserver (local-exec): ---> Package mailcap.noarch 0:2.1.41-2.amzn2 will be installed
aws_instance.prodserver (local-exec): ---> Package mod_http2.x86_64 0:1.15.19-1.amzn2.0.1 will be installed
aws_instance.prodserver (local-exec): --> Running transaction check
aws_instance.prodserver (local-exec): ---> Package apr-util-bdb.x86_64 0:1.6.3-1.amzn2.0.1 will be installed
aws_instance.prodserver (local-exec): --> Finished Dependency Resolution

aws_instance.prodserver (local-exec): Dependencies Resolved

aws_instance.prodserver (local-exec): ================================================================================
aws_instance.prodserver (local-exec):  Package                Arch      Version                   Repository     Size
aws_instance.prodserver (local-exec): ================================================================================
aws_instance.prodserver (local-exec): Installing:
aws_instance.prodserver (local-exec):  httpd                  x86_64    2.4.54-1.amzn2            amzn2-core    1.4 M
aws_instance.prodserver (local-exec): Installing for dependencies:
aws_instance.prodserver (local-exec):  apr                    x86_64    1.7.2-1.amzn2             amzn2-core    130 k
aws_instance.prodserver (local-exec):  apr-util               x86_64    1.6.3-1.amzn2.0.1         amzn2-core    101 k
aws_instance.prodserver (local-exec):  apr-util-bdb           x86_64    1.6.3-1.amzn2.0.1         amzn2-core     22 k
aws_instance.prodserver (local-exec):  generic-logos-httpd    noarch    18.0.0-4.amzn2            amzn2-core     19 k
aws_instance.prodserver (local-exec):  httpd-filesystem       noarch    2.4.54-1.amzn2            amzn2-core     24 k
aws_instance.prodserver (local-exec):  httpd-tools            x86_64    2.4.54-1.amzn2            amzn2-core     88 k
aws_instance.prodserver (local-exec):  mailcap                noarch    2.1.41-2.amzn2            amzn2-core     31 k
aws_instance.prodserver (local-exec):  mod_http2              x86_64    1.15.19-1.amzn2.0.1       amzn2-core    149 k

aws_instance.prodserver (local-exec): Transaction Summary
aws_instance.prodserver (local-exec): ================================================================================
aws_instance.prodserver (local-exec): Install  1 Package (+8 Dependent packages)

aws_instance.prodserver (local-exec): Total download size: 1.9 M
aws_instance.prodserver (local-exec): Installed size: 5.2 M
aws_instance.prodserver (local-exec): Downloading packages:
aws_instance.prodserver (local-exec): --------------------------------------------------------------------------------
aws_instance.prodserver (local-exec): Total                                              9.4 MB/s | 1.9 MB  00:00
aws_instance.prodserver (local-exec): Running transaction check
aws_instance.prodserver (local-exec): Running transaction test
aws_instance.prodserver (local-exec): Transaction test succeeded
aws_instance.prodserver (local-exec): Running transaction
aws_instance.prodserver (local-exec):   Installing : apr-1.7.2-1.amzn2.x86_64                                     1/9

aws_instance.prodserver (local-exec):   Installing : apr-util-1.6.3-1.amzn2.0.1.x86_64                            2/9
aws_instance.prodserver (local-exec):   Installing : apr-util-bdb-1.6.3-1.amzn2.0.1.x86_64                        3/9
aws_instance.prodserver (local-exec):   Installing : httpd-tools-2.4.54-1.amzn2.x86_64                            4/9

aws_instance.prodserver (local-exec):   Installing : httpd-filesystem-2.4.54-1.amzn2.noarch                       5/9
aws_instance.prodserver (local-exec):   Installing : generic-logos-httpd-18.0.0-4.amzn2.noarch                    6/9
aws_instance.prodserver (local-exec):   Installing : mailcap-2.1.41-2.amzn2.noarch                                7/9
aws_instance.prodserver (local-exec):   Installing : mod_http2-1.15.19-1.amzn2.0.1.x86_64                         8/9

aws_instance.prodserver (local-exec):   Installing : httpd-2.4.54-1.amzn2.x86_64                                  9/9

aws_instance.prodserver (local-exec):   Verifying  : apr-util-bdb-1.6.3-1.amzn2.0.1.x86_64                        1/9
aws_instance.prodserver (local-exec):   Verifying  : httpd-tools-2.4.54-1.amzn2.x86_64                            2/9
aws_instance.prodserver: Still creating... [40s elapsed]
aws_instance.prodserver (local-exec):   Verifying  : apr-1.7.2-1.amzn2.x86_64                                     3/9
aws_instance.prodserver (local-exec):   Verifying  : mod_http2-1.15.19-1.amzn2.0.1.x86_64                         4/9
aws_instance.prodserver (local-exec):   Verifying  : httpd-2.4.54-1.amzn2.x86_64                                  5/9
aws_instance.prodserver (local-exec):   Verifying  : apr-util-1.6.3-1.amzn2.0.1.x86_64                            6/9
aws_instance.prodserver (local-exec):   Verifying  : mailcap-2.1.41-2.amzn2.noarch                                7/9
aws_instance.prodserver (local-exec):   Verifying  : generic-logos-httpd-18.0.0-4.amzn2.noarch                    8/9
aws_instance.prodserver (local-exec):   Verifying  : httpd-filesystem-2.4.54-1.amzn2.noarch                       9/9


aws_instance.prodserver (local-exec): Installed:
aws_instance.prodserver (local-exec):   httpd.x86_64 0:2.4.54-1.amzn2

aws_instance.prodserver (local-exec): Dependency Installed:
aws_instance.prodserver (local-exec):   apr.x86_64 0:1.7.2-1.amzn2
aws_instance.prodserver (local-exec):   apr-util.x86_64 0:1.6.3-1.amzn2.0.1
aws_instance.prodserver (local-exec):   apr-util-bdb.x86_64 0:1.6.3-1.amzn2.0.1
aws_instance.prodserver (local-exec):   generic-logos-httpd.noarch 0:18.0.0-4.amzn2
aws_instance.prodserver (local-exec):   httpd-filesystem.noarch 0:2.4.54-1.amzn2
aws_instance.prodserver (local-exec):   httpd-tools.x86_64 0:2.4.54-1.amzn2
aws_instance.prodserver (local-exec):   mailcap.noarch 0:2.1.41-2.amzn2
aws_instance.prodserver (local-exec):   mod_http2.x86_64 0:1.15.19-1.amzn2.0.1

aws_instance.prodserver (local-exec): Complete!
aws_instance.prodserver: Creation complete after 40s [id=i-0a6aeea1011822b7d]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

Great Job!!! Keep it up
