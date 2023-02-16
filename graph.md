# Terraform Graph command

Using this utility we can see your configuration file converted to graphical image.

This utility used DOT language. You have to install the ```graphviz``` package.

### Always use help!!!

Help command provided the overall information about graph utility.

```
[root@ip-172-31-24-113 import]# terraform graph --help
Usage: terraform [global options] graph [options]

  Produces a representation of the dependency graph between different
  objects in the current configuration and state.

  The graph is presented in the DOT language. The typical program that can
  read this format is GraphViz, but many web services are also available
  to read this format.

Options:

  -plan=tfplan     Render graph using the specified plan file instead of the
                   configuration in the current directory.

  -draw-cycles     Highlight any cycles in the graph with colored edges.
                   This helps when diagnosing cycle errors.

  -type=plan       Type of graph to output. Can be: plan, plan-refresh-only,
                   plan-destroy, or apply. By default Terraform chooses
                                   "plan", or "apply" if you also set the -plan=... option.

  -module-depth=n  (deprecated) In prior versions of Terraform, specified the
                                   depth of modules to show in the output.

```

In our case we are using the amazon linux and the command ```yum install graphviz``` for installing the package.

```
[root@ip-172-31-24-113 import]# yum install graphviz
Loaded plugins: extras_suggestions, langpacks, priorities, update-motd
Resolving Dependencies
--> Running transaction check
---> Package graphviz.x86_64 0:2.30.1-21.amzn2.0.1 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

=======================================================================================================================================
 Package                      Arch                       Version                                  Repository                      Size
=======================================================================================================================================
Installing:
 graphviz                     x86_64                     2.30.1-21.amzn2.0.1                      amzn2-core                     1.3 M

Transaction Summary
=======================================================================================================================================
Install  1 Package

Total download size: 1.3 M
Installed size: 3.8 M
Is this ok [y/d/N]: y
Downloading packages:
graphviz-2.30.1-21.amzn2.0.1.x86_64.rpm                                                                         | 1.3 MB  00:00:00
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : graphviz-2.30.1-21.amzn2.0.1.x86_64                                                                                 1/1
  Verifying  : graphviz-2.30.1-21.amzn2.0.1.x86_64                                                                                 1/1

Installed:
  graphviz.x86_64 0:2.30.1-21.amzn2.0.1

Complete!
```

Command ```Terraform graph | dot -Tsvg > graph.jpg``` will help you to create a .jpg file of your configuration.

```
[root@ip-172-31-24-113 import]# ll
total 24
-rw-r--r-- 1 root root  294 Feb 16 07:08 ec2.tf
-rw-r--r-- 1 root root 3510 Feb 16 13:24 graph.jpg
-rw-r--r-- 1 root root  142 Feb 16 06:52 resource.tf
-rw-r--r-- 1 root root  180 Feb 16 13:17 terraform.tfstate
-rw-r--r-- 1 root root 4364 Feb 16 13:17 terraform.tfstate.backup
```

That's it. You did it. 
