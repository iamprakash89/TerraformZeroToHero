# Terraform Workspace

When you are are managing the multiple infrastructure/Resources you can utilize the workspace utility.

On real-time scenario, you may be end-up with multiple projects like dev,test,prod. Those workspace are separated and those having a separate variables.

using ```single.tf``` file you can manage a mutiple environments.

Get help from terraform workspace.
```
[root@terraform workspace]# terraform workspace --help
Usage: terraform [global options] workspace

  new, list, show, select and delete Terraform workspaces.

Subcommands:
    delete    Delete a workspace
    list      List Workspaces
    new       Create a new workspace
    select    Select a workspace
    show      Show the name of the current workspace
```
	
We have created a three workspace 

```
[root@terraform workspace]# terraform workspace new dev
Created and switched to workspace "dev"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.

[root@terraform workspace]# terraform workspace new test
Created and switched to workspace "test"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.

[root@terraform workspace]# terraform workspace new prod
Created and switched to workspace "prod"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
```

```terraform workspace show``` command will show the current workspace directory.

```
[root@terraform workspace]# terraform workspace show
prod
```

```terraform workspace list```  command will list all the workspace.

```
[root@terraform workspace]# terraform workspace list
  default
  dev
* prod
  test
```

When you create a workspace, terraform.tfstate.d directory will be created automatically and all the workspace are inside this directory.

```
[root@terraform workspace]# ll
total 8
-rw-r--r-- 1 root root 271 Feb 18 08:38 ec2.tf
-rw-r--r-- 1 root root 142 Feb 16 06:52 resource.tf
drwxr-xr-x 5 root root  41 Feb 22 17:27 terraform.tfstate.d
[root@terraform workspace]# cd terraform.tfstate.d/
[root@terraform terraform.tfstate.d]# ll
total 0
drwxr-xr-x 2 root root 6 Feb 22 17:26 dev
drwxr-xr-x 2 root root 6 Feb 22 17:27 prod
drwxr-xr-x 2 root root 6 Feb 22 17:27 test
```

We updated the ```.tf``` file and provided the instance_type as per the workspace.

```
[root@terraform workspace]# vi ec2.tf
[root@terraform workspace]# terraform validate
Success! The configuration is valid.

[root@terraform workspace]# cat ec2.tf
resource "aws_instance" "webserver" {
  ami                   = "ami-06e85d4c3149db26a"
  instance_type         = lookup(var.instance_type,terraform.workspace)
}

variable "instance_type" {
  type = map

default = {
  prod  = "t3.xlarge"
  dev = "t3.large"
  test = "t3.micro"
}
}
```

Here i switched between the workspaces and tried the ```terraform plan``` command 
According to the workspace, the ```instance_type``` has been changed. This means you can use the different variables to every workspace.

```
[root@terraform workspace]# terraform workspace select prod
[root@terraform workspace]# terraform workspace show
prod
[root@terraform workspace]# terraform plan | grep -i instance_type
      + instance_type                        = "t3.xlarge"
[root@terraform workspace]# terraform workspace select dev
Switched to workspace "dev".
[root@terraform workspace]# terraform workspace show
dev
[root@terraform workspace]# terraform plan | grep -i instance_type
      + instance_type                        = "t3.large"
[root@terraform workspace]# terraform workspace select test
Switched to workspace "test".
[root@terraform workspace]# terraform plan | grep -i instance_type
      + instance_type                        = "t3.micro"
```

You can apply and verify from your end.

You did a great Job!!!
