# managing-rhel-lifecycle-setup
This repo includes ansible playbooks and roles for setting up a demo environment of automating content management in Red Hat Satellite with Red Hat Ansible Automation Platform on AWS EC2.

## Environment to be set up
- Single VPC
- Single subnet
- Single route table
- Single gateway
- Single Satellite server
- Single managed server for dev and prod respectively
- Single Ansible Automation Controller

The setup looks like the following:

## Included contents
### Roles
|Name     |Description|
|:--------|:----------|
|satellite|A role to create a simple Satellite server on AWS EC2.|
|managed  |A role to create managed servers under the Satellite server's control on AWS EC2.|
|aac      |A role to create an Ansible Automation Controller on AWS EC2.|

### Playbooks
|Name     |Role Used|Description|
|:--------|:--------|:----------|
|`create_networks.yml`|N/A|Create required AWS network resources.|
|`delete_networks.yml`|N/A|Delete AWS network resources created in `create_networks` playbook.|
|`create_satellite_vm.yml`|[roles.satellite](roles/satellite/README.md)|Create an AWS instance and set up Satellite server.|
|`delete_satellite_vm.yml`|N/A|Delete the instance created in `create_satellite_vm` playbook.|
|`create_managed_vms.yml`|[roles.managed](roles/managed/README.md)|Create AWS instances and set up managed servers.|
|`delete_managed_vms.yml`|N/A|Delete the instances created in `create_managed_vms` playbook.|
|`create_aac_vm.yml`|[roles.aac](roles/aac/README.md)|Create an AWS instance and set up Ansible Automation Controller.|
|`delete_aac_vm.yml`|N/A|Delete the instance created in `create_aac_vm` playbook.|


## Prerequisites
### Basic requirements for Ansible
Any control node:
- ansible core 2.15+

Ansible collections:
- amazon.aws
- awx.awx
- community.crypto
- community.mysql
- redhat.rhel_system_roles
- redhat.satellite_operations
- redhat.satellite

In order for functioning Ansible EC2, you need to install Python Boto3 library.
```
# pip3 install boto3
```
### ansible.cfg
Update the following line with your EC2 private key file.
```
[default]
private_key_file = "path to EC2 private key file"
```
### Environment variables
In this setup, you should set the following environment variables on your control node.
```
$ export AWS_DEFAULT_REGION=ap-northeast-1
$ export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
$ export AWS_SECRET_ACCESS_KEY=wJatrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
```

### Subscriptions
Basically, the demo environment requires to get access to Red Hat gold image, so you must have a matching Red Hat product subscription and must connect their cloud provider accounts to Red Hat.
Please refer to the [Cloud Access user interface](https://access.redhat.com/management/cloud) or Cloud Sources on [cloud.redhat.com](https://cloud.redhat.com/) as described in [Chapter 2, Getting started with Cloud Access](https://access.redhat.com/documentation/en-us/subscription_central/2023/html/red_hat_cloud_access_reference_guide/getting-started-with-ca_cloud-access).

Required subscriptions:
- Red Hat Enterprise Linux for x86_64
- Red Hat Satellite Infrastructure Subscription
- Red Hat Ansible Automation Platform Subscription

## Usage
### Create required network resources
These variables should be set in group_vars beforehand.
```
aws_vpc: demo_vpc
aws_vpc_cidr_block: 10.0.0.0/16 # adjust with your preference
aws_vpc_subnet_name: demo_subnet
aws_subnet_cidr_block: 10.0.1.0/24 # adjust with your preference
aws_igw_name: demo_gtw
aws_routetable_name: demo_rtb
aws_securitygroup_name: demo_sg

purpose: demo
```

This playbook need to be run at the beginning.
```
$ ansible-playbook create_networks.yml
```

### Create Satellite server
These variables should be set in group_vars beforehand.
```
aws_vpc_subnet_name: demo_subnet
aws_securitygroup_name: demo_sg
aws_satellite_instance_ami: ami-08c8784e357ec2d8b # ami of RHEL-8.8.0_HVM-20230802-x86_64-64-Access2-GP2
aws_satellite_instance_size: t2.2xlarge # should not be modified

satellite_vm_type: satellite # should not be modified
satellite_vm_environment: mgmt # should not be modified
satellite_vm_name: satellite01

purpose: demo
```

And, the following variables are prompted at run-time. Also refer to [roles.satellite](roles/satellite/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
foreman_admin_passwd # Password for your Satellite admin user
```

This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_satellite_vm.yml
```

### Create managed servers
These variables should be set in group_vars beforehand.
```
aws_vpc_subnet_name: demo_subnet
aws_securitygroup_name: demo_sg
aws_managed_instance_ami: ami-04fd9e98c56fe4d28 # ami of RHEL-9.2.0_HVM-20230615-x86_64-3-Access2-GP2
aws_managed_instance_size: t2.small # can be bigger instance size

managed_vms_type: managed # should not be modified
managed_vms_name_prefix: managed # should not be modified
managed_vms_environment:
  - dev # should not be modified
  - prod # should not be modified

purpose: demo
```

And, the following variables are prompted at run-time.  Also refer to [roles.managed](roles/managed/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
foreman_admin_passwd # Password for your Satellite admin user
mysql_root_passwd # MySQL root password for WordPress
mysql_wp_passwd # MySQL user password for WordPress
```

This playbook can run after running `create_satellite_vm` playbook.
```
$ ansible-playbook create_managed_vms.yml
```

### Create Ansible Automation Controller
These variables should be set in group_vars beforehand.
```
aws_vpc_subnet_name: demo_subnet
aws_securitygroup_name: demo_sg
aws_aac_instance_ami: ami-04fd9e98c56fe4d28 # ami of RHEL-9.2.0_HVM-20230615-x86_64-3-Access2-GP2
aws_aac_instance_size: t2.xlarge # should not be modified

aac_vm_type: aac # should not be modified
aac_vm_environment: mgmt # should not be modified
aac_vm_name: aac01

purpose: demo
```

And, the following variables are prompted at run-time. Also refer to [roles.aac](roles/aac/README.md) for the role details.
```
aws_keypair_name # Your AWS key pair name corresponding to the private key
rhsm_username # Your Red Hat login name
rhsm_passwd # Password for your Red Hat login
aac_admin_passwd # Password for your AAC admin user
aac_pg_passwd # PostgreSQL password for your AAC deployment
```

This playbook can run after running `create_networks` playbook.
```
$ ansible-playbook create_aac_vm.yml
```
### Clean up the environment
All the delete resource playbooks corresponding to each create resource playbook are avaialble. Those playbooks can run assuming related variables have already set previously.