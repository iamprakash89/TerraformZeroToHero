# Terraform Installation in AWS EC2 instance.

Pre-requistics
where you are executing the terraform, the server should have a internet connection and able to access publically.

OS: Amazon Linux 2

Ec2 instance details.
```
[root@terraform ~]# date;hostname
Sat Feb 11 08:52:26 UTC 2023
terraform
```

Installing a terraform proceed by below three steps.

Step 1] Install the amazon extra packages command using 
```yum install -y yum-utils shadow-utils```    

Step 2] Add the hashicorp repository in Amazon Ec2 Instance server using 
```yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo```

Step 3] Install the latest terrafrom version from the respective repository using
```yum -y install terraform```

### The below outputs for how we installed the terraform using the above three steps

```
[root@terraform ~]# yum install -y yum-utils shadow-utils
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Package yum-utils-1.1.31-46.amzn2.0.1.noarch already installed and latest version
Package 2:shadow-utils-4.1.5.1-24.amzn2.0.2.x86_64 already installed and latest version
Nothing to do

[root@terraform ~]# yum-config-manager --add-repo https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
adding repo from: https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo
grabbing file https://rpm.releases.hashicorp.com/AmazonLinux/hashicorp.repo to /etc/yum.repos.d/hashicorp.repo
repo saved to /etc/yum.repos.d/hashicorp.repo

[root@terraform ~]# yum -y install terraform
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
hashicorp                                                                                                       | 1.4 kB  00:00:00
hashicorp/x86_64/primary                                                                                        | 143 kB  00:00:00
hashicorp                                                                                                                    1033/1033
Resolving Dependencies
--> Running transaction check
---> Package terraform.x86_64 0:1.3.8-1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================================================================
 Package                          Arch                          Version                         Repository                        Size
=======================================================================================================================================
Installing:
 terraform                        x86_64                        1.3.8-1                         hashicorp                         13 M

Transaction Summary
=======================================================================================================================================
Install  1 Package

Total download size: 13 M
Installed size: 58 M
Downloading packages:
warning: /var/cache/yum/x86_64/2/hashicorp/packages/terraform-1.3.8-1.x86_64.rpm: Header V4 RSA/SHA256 Signature, key ID a621e701: NOKEY
Public key for terraform-1.3.8-1.x86_64.rpm is not installed
terraform-1.3.8-1.x86_64.rpm                                                                                    |  13 MB  00:00:00
Retrieving key from https://rpm.releases.hashicorp.com/gpg
Importing GPG key 0xA621E701:
 Userid     : "HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>"
 Fingerprint: 798a ec65 4e5c 1542 8c8e 42ee aa16 fcbc a621 e701
 From       : https://rpm.releases.hashicorp.com/gpg
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : terraform-1.3.8-1.x86_64                                                                                            1/1
  Verifying  : terraform-1.3.8-1.x86_64                                                                                            1/1

Installed:
  terraform.x86_64 0:1.3.8-1

Complete!
```

Verifying the terraform installed version.
```
[root@terraform ~]# terraform -v
Terraform v1.3.8
on linux_amd64
```

Thats is You have installed the terraform in Amazon Ec2 server successfully.
