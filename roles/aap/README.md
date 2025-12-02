Role Name
=========

This role "aap" is designed to create an Ansible Automation Platform on AWS EC2 for demo purpose.

Requirements
------------

Basically, the role assumes to setup a containerized Ansible Automation Platform using Red Hat Cloud Access Gold Images on AWS EC2. 

The tested environment:
- RHEL-9.6.0_HVM-20250910-x86_64-0-Hourly2-GP3
- Ansible Automation Platform 2.6

A Red Hat Account and related Red Hat Ansible Automation Platform Subscription are requird, because you need to supply a manifest file linked to the subscription.

Also, Ansible Automation Platform's setup file need to be downloaded from ["Download Red Hat Ansible Automation Platform"](https://access.redhat.com/downloads/content/480/ver=2.6/rhel---9/2.6/x86_64/product-software) beforehand.

Role Variables
--------------

var/main.yml includes the following pre-set variables. You should change them adequately.
- aap_local_installer_path
- aap_base_dir
- aap_installer_dest_path

Also, the following variables should be supplied when using the role. In this project, upper playbooks are supposed to set these variables.
- rhsm_username
- rhsm_passwd
- aap_private_dns_name
- aap_admin_passwd
- aap_pg_passwd

Dependencies
------------

The following collections need to be installed beforehand. You can install those collections by using requirements.yml located in the project top directry.
- redhat.rhel_system_roles

Example Playbook
----------------

    - name: Configure AAP server
      hosts: aap
      gather_facts: true
      vars:
        aap_private_dns_name: "{{ hostvars.localhost.private_dns_name }}"

      vars_prompt:
        - name: rhsm_username
          prompt: "What is your Red Hat login name?"
          private: false
        - name: rhsm_passwd
          prompt: "What is your Red Hat login password?"
        - name: aap_admin_passwd
          prompt: "Enter your AAP admin password"
        - name: aap_pg_passwd
          prompt: "Enter your PostgreSQL password for AAP"

      roles:
        - aap


License
-------

MIT

Author Information
------------------

Yukiya Shimizu
https://github.com/yukshimizu
