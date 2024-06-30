Role Name
=========

This role "satellite" is designed to create a simple satellite server on AWS EC2 for demo purpose.

Requirements
------------

Basically, the role assumes to setup Sattelite using Red Hat Cloud Access Gold Images on AWS EC2. 

The tested environment:
- RHEL-8.8.0_HVM-20230802-x86_64-64-Access2-GP2
- Satellite 6.13 and 6.15

A Red Hat Account and related Red Hat Satellite Infrastructure Subscription are requird, because you need to supply a manifest file linked to the subscription.

Role Variables
--------------

var/main.yml includes the following pre-set variables. You should change them adequately.
- local_manifest_path
- foreman_manifest_path
- foreman_admin_username
- foreman_organization
- foreman_location
- foreman_cv_initial_end_date

Also, the following variables should be supplied when using the role. In this project, upper playbooks are supposed to set these variables.
- rhsm_username
- rhsm_passwd
- foreman_admin_passwd
- satellite_public_dns_name

Dependencies
------------

The following collections need to be installed beforehand. You can install those collections by using requirements.yml located in the project top directry.
- redhat.rhel_system_roles
- redhat.satellite_operations
- redhat.satellite

Example Playbook
----------------

    - name: Configure satellite server
      hosts: satellite
      become: true
      gather_facts: true
      vars:
        satellite_public_dns_name: "public dns name of satellite server"

      vars_prompt:
        - name: rhsm_username
          prompt: "What is your Red Hat login name?"
          private: false
        - name: rhsm_passwd
          prompt: "What is your Red Hat login password?"
        - name: foreman_admin_passwd
          prompt: "Enter your Satellite admin password"

      roles:
        - satellite

License
-------

MIT

Author Information
------------------

Yukiya Shimizu
https://github.com/yukshimizu
