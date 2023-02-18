# Terraform Debugging

Using the ```Terraform Utility``` we can troubleshoot the issue using the debug logs. 

### There are two options available.

1) TF_LOG  -> This parameter will display the debug logs when using the ```terraform plan``` command. 
     Ex: export TF_LOG = WARN/DEBUG/INFO/TRACE/ERROR
	 
2) TF_LOG_PATH -> We can provide a file path where you want to store your logs.
   Ex: export TF_LOG_PATH=/tmp/filename

On this example, I am going to enable the logs from the server end and show how is that works.

```
[root@ip-172-31-24-113 import]# export TF_LOG = WARN
```
As per the below logs, we can able to see the logs steps what is happening when you are running the script.

```
[root@ip-172-31-24-113 import]# terraform destroy
aws_instance.prodserver: Refreshing state... [id=i-0e37781f6c445a44e]
2023-02-18T10:42:34.768Z [WARN]  Provider "registry.terraform.io/hashicorp/aws" produced an unexpected new value for aws_instance.prodserver during refresh.
      - .tags: was null, but now cty.MapValEmpty(cty.String)
2023-02-18T10:42:34.779Z [WARN]  Provider "registry.terraform.io/hashicorp/aws" produced an invalid plan for aws_instance.prodserver, but we are tolerating it because it is using the legacy plugin SDK.
    The following problems may be the cause of any confusing errors from downstream operations:
      - .tags: planned value cty.MapValEmpty(cty.String) for a non-computed attribute
      - .hibernation: planned value cty.False for a non-computed attribute
      - .source_dest_check: planned value cty.True for a non-computed attribute
      - .user_data_replace_on_change: planned value cty.False for a non-computed attribute
      - .get_password_data: planned value cty.False for a non-computed attribute
      - .maintenance_options: block count in plan (1) disagrees with count in config (0)
      - .metadata_options: block count in plan (1) disagrees with count in config (0)
      - .private_dns_name_options: block count in plan (1) disagrees with count in config (0)
      - .capacity_reservation_specification: block count in plan (1) disagrees with count in config (0)
      - .credit_specification: block count in plan (1) disagrees with count in config (0)
      - .enclave_options: block count in plan (1) disagrees with count in config (0)
      - .root_block_device: block count in plan (1) disagrees with count in config (0)
	  [.....]
Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy all resources?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

aws_instance.prodserver: Destroying... [id=i-0e37781f6c445a44e]
aws_instance.prodserver: Still destroying... [id=i-0e37781f6c445a44e, 10s elapsed]
```

change with other ```TF_LOGS``` option and try it from your end.

Note: Even of you apply a TF_LOG_PATH, It must be configured with TF_LOG.

```
[root@ip-172-31-24-113 import]# export TF_LOG_PATH=/tmp/terrform.log
[root@ip-172-31-24-113 import]# terraform plan

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the following
symbols:
  + create

Terraform will perform the following actions:

  # aws_instance.prodserver will be created
[.....]
```

- The log shows, terraform.log having a enough details to verify what is happening from the background.

```
[root@ip-172-31-24-113 import]# cat /tmp/terrform.log
2023-02-18T10:46:24.569Z [WARN]  Provider "registry.terraform.io/hashicorp/aws" produced an invalid plan for aws_instance.prodserver, but we are tolerating it because it is using the legacy plugin SDK.
    The following problems may be the cause of any confusing errors from downstream operations:
      - .get_password_data: planned value cty.False for a non-computed attribute
      - .source_dest_check: planned value cty.True for a non-computed attribute
      - .user_data_replace_on_change: planned value cty.False for a non-computed attribute
      - .metadata_options: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .ebs_block_device: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .ephemeral_block_device: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .maintenance_options: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .network_interface: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .private_dns_name_options: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .root_block_device: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .capacity_reservation_specification: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
      - .enclave_options: attribute representing nested block must not be unknown itself; set nested attribute values to unknown instead
```

Great, Job. You did it.
