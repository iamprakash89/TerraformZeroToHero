# What is Files format

In terraform we need to use .tf or .tf.json extension to represent the terraform files. 
When you are executing the terraform command the .tf file will be recognised.
On the directory you may have a single file contians all the configuration or multiple files will be created. 
It depends on you, how you want to keep the information.

example ```ec2_create.tf``` or ```config.tf```

# What is directories and modules

Module is noting but the files are in inside of .tf files or you can call it a collection of .tf file.

### Root Module:
For example: you have a terraform directory which contains a .tf files(root modules) a
So when you are executing the terraform command from the directory, terraform always recognised the main .tf files and run accordingly. 

On the below output prodapp directory created and added the ```.tf``` files. In this case ```ec2_create.tf``` file is a root module.

```
[root@terraform prodapp]# mkdir /opt/prodapp
[root@terraform prodapp]# pwd
/opt/prodapp
[root@terraform prodapp]# vi ec2_create.tf <= Root module
[root@terraform prodapp]# vi resource.tf
[root@terraform prodapp]# ls -la
total 0
drwxr-xr-x 2 root root 46 Feb 11 14:12 .
drwxr-xr-x 5 root root 42 Feb 11 14:10 ..
-rw-r--r-- 1 root root  0 Feb 11 14:12 ec2_create.tf
-rw-r--r-- 1 root root  0 Feb 11 14:12 resource.tf
```
Continuing on next chapter for how to initialize the directory using terraform.
