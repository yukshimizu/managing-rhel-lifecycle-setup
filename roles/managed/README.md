Role Name
=========

This role "managed" is designed to create managed servers under a Satellite server's control on AWS EC2 for demo purpose. Those servers will be registered to the Satellite , and also be installed a demo application (WordPress) and required packages.

Requirements
------------

Basically, the role assumes to setup RHEL servers using Red Hat Cloud Access Gold Images on AWS EC2. 

The tested environment:
- RHEL-9.2.0_HVM-20230615-x86_64-3-Access2-GP2
- with Satellite 6.13
- WordPress 6.3.2
- MySQL 8.0

Role Variables
--------------

var/main.yml includes the following pre-set variables. You should change them adequately.
- foreman_admin_username
- foreman_organization
- foreman_location
- foreman_lifecycle
- foreman_content_view
- mysql_wp_user
- wp_archive_url
- wp_weblog_title
- wp_user_name
- wp_admin_password
- wp_allow_weak_pass
- wp_admin_email
- wp_blog_public

Also, the following variables should be supplied when using the role. In this project, upper playbooks are supposed to set these variables.
- satellite_private_dns_name
- foreman_admin_passwd
- foreman_lifecycle
- mysql_root_passwd
- mysql_wp_passwd

Dependencies
------------

The following collections need to be installed beforehand. You can install those collections by using requirements.yml located in the project top directry.
- community.crypto
- community.mysql
- redhat.rhel_system_roles

Example Playbook
----------------

    - name: Configure dev server
      hosts: dev
      become: true
      gather_facts: true
      vars:
        foreman_lifecycle: "Development"
        satellite_private_dns_name: "private dns name of satellite server"

      vars_prompt:
        - name: foreman_admin_passwd
          prompt: "Enter your Satellite admin password"
        - name: mysql_root_passwd
          prompt: "Enter your MySQL root password for WordPress"
        - name: mysql_wp_passwd
          prompt: "Enter your MySQL user password for WordPress"

      roles:
      - managed

License
-------

MIT

Author Information
------------------

Yukiya Shimizu
https://github.com/yukshimizu
