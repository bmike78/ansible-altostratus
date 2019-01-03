.. _configuration:

Configuration
=============

Ansible
-------

Virtual Environment
~~~~~~~~~~~~~~~~~~~
It is recommended that a Python virtual environment be used for ansible-altostratus.  When you download playbooks from Galaxy, they may contain newer modules or bug fixes to existing modules.  For example, the modules for Infoblox, docker and Openstack all have recent changes, so Ansible 2.7.1 or greater is required if using playbook/roles that use those modules.

Using ansible.cfg
~~~~~~~~~~~~~~~~~
ansible.cfg comes with the repo which changes the location of where Galaxy roles are downloaded.  

