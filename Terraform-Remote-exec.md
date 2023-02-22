# Terraform Remote-Exec

When you want to run some scripts or commands inside of the server, we need to use the ```terraform remote-exec``` utility.

First you have to move your script from local to server then using ```file provisoner``` and we can perform the required activity(such as execution/starting some services).

Second we run the script using ```remote-exec``` provisioner.

On this example we are going to create a EC2 instance and passing a file from local to server and start apache service and move the file to respective location.

```
[root@terraform remote-exec]# pwd
/opt/remote-exec
[root@terraform remote-exec]# ll
total 12
-r-------- 1 root root 1679 Feb 21 15:28 demo.pem
-rw-r--r-- 1 root root  877 Feb 22 12:49 ec2.tf
-rw-r--r-- 1 root root  142 Feb 16 06:52 resource.tf
-rw-r--r-- 1 root root   54 Feb 22 12:57 index.html
```

As per the below logs, new ec2 instance is going to be created and one index file copying from terraform server to EC2 instance.
After that using ```remote-exec``` option we are installing a apache and deploying a simple index page.

```
[root@terraform remote-exec]# cat ec2.tf
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
      "yum install httpd -y",
      "systemctl start httpd.service",
      "systemctl enable httpd.service",
      "cp /tmp/index.html /var/www/html/index.html",
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

Let's apply to validate the results. ```terraform apply```


```
[root@terraform remote-exec]# terraform apply

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
aws_instance.prodserver: Still creating... [30s elapsed]
aws_instance.prodserver: Provisioning with 'file'...
aws_instance.prodserver: Provisioning with 'remote-exec'...
aws_instance.prodserver (remote-exec): Connecting to remote host via SSH...
aws_instance.prodserver (remote-exec):   Host: 52.11.68.118
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
aws_instance.prodserver (remote-exec): Existing lock /var/run/yum.pid: another copy is running as pid 3199.
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory :  87 M RSS (379 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:05 ago
aws_instance.prodserver (remote-exec):     State  : Sleeping, pid: 3199
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory :  97 M RSS (390 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:07 ago
aws_instance.prodserver (remote-exec):     State  : Running, pid: 3199
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory :  97 M RSS (390 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:09 ago
aws_instance.prodserver (remote-exec):     State  : Running, pid: 3199
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory : 196 M RSS (489 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:11 ago
aws_instance.prodserver (remote-exec):     State  : Running, pid: 3199
aws_instance.prodserver: Still creating... [40s elapsed]
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory : 178 M RSS (470 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:13 ago
aws_instance.prodserver (remote-exec):     State  : Running, pid: 3199
aws_instance.prodserver (remote-exec): Existing lock /var/run/yum.pid: another copy is running as pid 3221.
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory : 101 M RSS (319 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:15 ago
aws_instance.prodserver (remote-exec):     State  : Running, pid: 3221
aws_instance.prodserver (remote-exec): Another app is currently holding the yum lock; waiting for it to exit...
aws_instance.prodserver (remote-exec):   The other application is: yum
aws_instance.prodserver (remote-exec):     Memory : 128 M RSS (365 MB VSZ)
aws_instance.prodserver (remote-exec):     Started: Wed Feb 22 13:03:53 2023 - 00:17 ago
aws_instance.prodserver (remote-exec):     State  : Sleeping, pid: 3221
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
aws_instance.prodserver (remote-exec): (1/9): apr-util-1. | 101 kB   00:00
aws_instance.prodserver (remote-exec): (2/9): apr-1.7.2-1 | 130 kB   00:00
aws_instance.prodserver (remote-exec): (3/9): apr-util-bd |  22 kB   00:00
aws_instance.prodserver (remote-exec): (4/9): generic-log |  19 kB   00:00
aws_instance.prodserver (remote-exec): (5/9): httpd-files |  24 kB   00:00
aws_instance.prodserver (remote-exec): (6/9): httpd-2.4.5 | 1.4 MB   00:00
aws_instance.prodserver (remote-exec): (7/9): mailcap-2.1 |  31 kB   00:00
aws_instance.prodserver (remote-exec): (8/9): httpd-tools |  88 kB   00:00
aws_instance.prodserver (remote-exec): (9/9): mod_http2-1 | 149 kB   00:00
aws_instance.prodserver (remote-exec): ----------------------------------------
aws_instance.prodserver (remote-exec): Total      9.1 MB/s | 1.9 MB  00:00
aws_instance.prodserver (remote-exec): Running transaction check
aws_instance.prodserver (remote-exec): Running transaction test
aws_instance.prodserver (remote-exec): Transaction test succeeded
aws_instance.prodserver (remote-exec): Running transaction
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [         ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [##       ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [####     ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [######   ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [#######  ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7. [######## ] 1/9
aws_instance.prodserver (remote-exec):   Installing : apr-1.7.2-1.amzn2.   1/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [         ] 2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [##       ] 2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [#####    ] 2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [#######  ] 2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [######## ] 2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util-1.6.3-1.a   2/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [         ] 3/9
aws_instance.prodserver (remote-exec):   Installing : apr-util [######## ] 3/9
aws_instance.prodserver (remote-exec):   Installing : apr-util-bdb-1.6.3   3/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [         ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [##       ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [###      ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [####     ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [#####    ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [######   ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-to [######## ] 4/9
aws_instance.prodserver (remote-exec):   Installing : httpd-tools-2.4.55   4/9
aws_instance.prodserver (remote-exec):   Installing : generic- [         ] 5/9
aws_instance.prodserver (remote-exec):   Installing : generic- [#######  ] 5/9
aws_instance.prodserver (remote-exec):   Installing : generic- [######## ] 5/9
aws_instance.prodserver (remote-exec):   Installing : generic-logos-http   5/9
aws_instance.prodserver (remote-exec):   Installing : mailcap- [         ] 6/9
aws_instance.prodserver (remote-exec):   Installing : mailcap- [#######  ] 6/9
aws_instance.prodserver (remote-exec):   Installing : mailcap- [######## ] 6/9
aws_instance.prodserver (remote-exec):   Installing : mailcap-2.1.41-2.a   6/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [         ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [#        ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [###      ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [####     ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [#####    ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [######   ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [#######  ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-fi [######## ] 7/9
aws_instance.prodserver (remote-exec):   Installing : httpd-filesystem-2   7/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [         ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [#        ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [##       ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [####     ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [#####    ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [#######  ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http [######## ] 8/9
aws_instance.prodserver (remote-exec):   Installing : mod_http2-1.15.19-   8/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [         ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [#        ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [##       ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [###      ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [####     ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [#####    ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [######   ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [#######  ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2. [######## ] 9/9
aws_instance.prodserver (remote-exec):   Installing : httpd-2.4.55-1.amz   9/9
aws_instance.prodserver (remote-exec):   Verifying  : apr-util-bdb-1.6.3   1/9
aws_instance.prodserver (remote-exec):   Verifying  : httpd-2.4.55-1.amz   2/9
aws_instance.prodserver (remote-exec):   Verifying  : apr-1.7.2-1.amzn2.   3/9
aws_instance.prodserver (remote-exec):   Verifying  : httpd-filesystem-2   4/9
aws_instance.prodserver (remote-exec):   Verifying  : mailcap-2.1.41-2.a   5/9
aws_instance.prodserver (remote-exec):   Verifying  : generic-logos-http   6/9
aws_instance.prodserver (remote-exec):   Verifying  : mod_http2-1.15.19-   7/9
aws_instance.prodserver (remote-exec):   Verifying  : httpd-tools-2.4.55   8/9
aws_instance.prodserver (remote-exec):   Verifying  : apr-util-1.6.3-1.a   9/9

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
aws_instance.prodserver: Still creating... [50s elapsed]
aws_instance.prodserver: Creation complete after 50s [id=i-08e169611bad45778]

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

We try to looged in with the credntials and ip address of the ec2 instance and verified services & file are deployed properly.

```
[root@terraform remote-exec]# ssh -i demo.pem ec2-user@52.11.68.118
The authenticity of host '52.11.68.118 (52.11.68.118)' can't be established.
ECDSA key fingerprint is SHA256:DXfRIDPFyfG0H540J+n0JqrVlmEpxIZ3iim0/sc0Npc.
ECDSA key fingerprint is MD5:24:91:7e:9e:9d:0e:63:47:74:98:71:79:15:bb:a6:d9.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '52.11.68.118' (ECDSA) to the list of known hosts.
Last login: Wed Feb 22 13:03:57 2023 from 35.85.53.90

       __|  __|_  )
       _|  (     /   Amazon Linux 2 AMI
      ___|\___|___|

https://aws.amazon.com/amazon-linux-2/
16 package(s) needed for security, out of 19 available
Run "sudo yum update" to apply all updates.
[ec2-user@ip-172-31-17-11 ~]$ sudo -i
[root@ip-172-31-17-11 ~]# clear
[root@ip-172-31-17-11 ~]# ll
total 0
[root@ip-172-31-17-11 ~]# cd /var/www/html/
[root@ip-172-31-17-11 html]# ll
total 4
-rw-r--r-- 1 root root 54 Feb 22 13:04 index.html
[root@ip-172-31-17-11 html]# curl http://localhost
HELLO WORLD

TERRAFORM WEB DEPLOYMENT

YOU DID IT!!!!
[root@ip-172-31-17-11 html]# exit
logout
[ec2-user@ip-172-31-17-11 ~]$ exit
logout
Connection to 52.11.68.118 closed.
[root@terraform remote-exec]# curl http://52.11.68.118
^C
[root@terraform remote-exec]#
```

Nice Job!!! Great Work!!!
