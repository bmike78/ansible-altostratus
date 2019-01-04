.. _faq:

FAQ
===

What is Altostratus ?
---------------------
Altostratus_ combines Ansible_ playbook roles with cloud resources, such as Openstack and CloudFoundry which takes a different approach to launching and managing resources:

- It uses Ansible which can be used to create more complex workflows and piece together different pieces of infrastructure
- It uses reusable tasks and roles that can be used in multiple clouds (openstack, cloudfoundry, vmware, AWS, azure)
- It throws away a bunch of custom cloud code and is replaced with easy to read yaml files and playbooks.
- It allows for mixing of roles to deploy resources and applications to multiple clouds or instances.
- It allows for reconfiguration of existing resources using Ansible's built in idempotence capabilities for Ansible modules.

.. _Altostratus: https://github.com/bmike78/ansible-altostratus
.. _Ansible: https://www.ansible.com/


Why is it called Altostratus ?
------------------------------
Altostratus - a layer cloud at an intermediate height of about 2400 to 6000 metres (8000 to 20 000 feet).  The playbook sits above all your clouds, but can be used by interfaces above it, like ansible-playbook, API, Jenkins, Rundeck, Ansible Tower, AWX or Vespene.

What does the interface look like ?
-----------------------------------
The interface is console based and run using "ansible-playbook".  There is no web interface yet.

What versions of Ansible are supported ?
----------------------------------------
This really depends on what Ansible roles and Openstack features are used.  It is suggested that you run at least Ansible 2.7 and the latest openstacksdk to allow the "reboot" and "docker swarm" modules and some fixes to the Designate os_recordset Ansible module.  These python packages and versions needed are listed in the requirements.txt file.

Does Altostratus support running on Python 3 ?
----------------------------------------------
Probably. 

Why is Altostratus being developed?
-----------------------------------
Ansible is an awesome tool. It can be used to automate many things that make it solution and vendor agnostic.  
Ansible roles help get applications installed and configured, but it doesn't include any infrastructure creation/modification/deletion components.

Openstack is an great cloud platform for hosting instances, containers and other infrastructure components like security groups, load balancers, DNS records.  
The Openstack API and openstacksdk allows access to use those resources in a programatic way and the Ansible modules for Openstack use the openstacksdk. 

CloudFoundry is a great iaas platform for hosting java, python, PHP applications.
Besides the manifest file, you have to run a couple of commands to switch between organizations and spaces and modify repos if you're dropping custom configs/credentials into your different spaces.

Learning Ansible, Openstack and CloudFoundry takes a lot of time and putting all these pieces together for a coherent application deployment can be difficult.   

Altostratus aims to do one thing and do it well: Create cloud resources, then deploy applications using Openstack or CloudFoundry in an automated way with the configuration of a YAML file.  Idempotency in Ansible allows you to define or redefine the YAML config for the application and run the playbook over and over again to get to the desired state.

Does Altostratus support other clouds like AWS, Azure, VMware, etc ?
--------------------------------------------------------------------
Not at this time, but functionality could be added later to do this with additional Ansible roles.  Some decent roles or playbooks may exist that could be imported. 

