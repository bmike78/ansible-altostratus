.. _applications:

Applications
============

Ansible playbooks for launching applications in Openstack

Application Roles
--------------
Application roles can either be custom apps or downloaded from Ansible Galaxy.

Custom roles are stored in roles/apps/
Ansible Galaxy roles are stored in roles/galaxy/.  This location is defined in ansible.cfg::

  server1:/etc/ansible/ansible-altostratus/roles/apps
  # ls
  ansible  apache  disk-performance


Application Role Variables
--------------
Application variables are defined in::
 
  group_vars/apps/<role>/main.yml

Playbook Roles
--------------
::

  ansible - installs ansible from custom role
  apache - installs and configures apache from Ansible Galaxy role   geerlingguy.apache
  disk-performance - copies disk-performance scripts (uses dd) and runs locally with output from custom role
  docker - installs and configures docker from Ansible Galaxy role   geerlingguy.docker
  dockerswarm - installs and configures docker from Ansible Galaxy role asg1612.dockerswarm
  elasticsearch - installs and configures elasticsearch from Ansible Galaxy role geerlingguy.elasticsearch
  git - installs and configures git from Ansible Galaxy role geerlingguy.git
  haproxy - installs and configures haproxy from Ansible Galaxy role geerlingguy.haproxy
  icinga2-server - installs and configures custom role icinga2-server.
  java - installs and configures haproxy from Ansible Galaxy role geerlingguy.java
  jenkins - installs and configures jenkins from Ansible Galaxy role geerlingguy.jenkins
  kibana - installs and configures kibana from Ansible Galaxy role geerlingguy.kibana
  kubernetes - installs and configures kubernetes cluster from Ansible Galaxy role geerlingguy.kubernetes
  logstash - installs and configures logstash from Ansible Galaxy role geerlingguy.logstash
  mysql - installs and configures mysql from Ansible Galaxy role geerlingguy.mysql
  postgresql - installs and configures postgresql from Ansible Galaxy role geerlingguy.postgresql
  redis - installs and configures redis from Ansible Galaxy role geerlingguy.redis
  rhel7-cis - configures server for CIS compliance for RHEL/CentOS7 from Ansible Galaxy role mindpointgroup.rhel7-cis
  solr - installs and configures solr from Ansible Galaxy role geerlingguy.solr
  sonar - installs and configures sonar from Ansible Galaxy role geerlingguy.sonar
  ubuntu1604-cis - configures server for CIS compliance for Ubuntu 16.04 from Ansible Galaxy role florianutz.ubuntu1604-cis


Ansible Galaxy
--------------

Requirements
~~~~~~~~~~~~
There is a requirements file called requirements.yml that has all the Ansible Galaxy roles used in the file applications.yml file.

Ansible Galaxy Download Roles
-----------------------------
To download the galaxy roles, run::

  # ansible-galaxy install -r requirements.yml
  [WARNING]: - geerlingguy.apache (3.0.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.docker (2.5.1) is already installed - use --force to change version to unspecified

  [WARNING]: - asg1612.dockerswarm (master) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.elasticsearch (2.2.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.git (2.0.2) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.haproxy (1.1.2) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.java (1.9.2) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.jenkins (3.6.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.kibana (3.1.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.logstash (3.0.1) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.mysql (2.9.3) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.pip (1.2.2) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.postgresql (1.4.3) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.redis (1.6.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.solr (4.3.0) is already installed - use --force to change version to unspecified

  [WARNING]: - geerlingguy.sonar (1.4.1) is already installed - use --force to change version to unspecified

  server1:/etc/ansible/ansible-altostratus/roles/galaxy
  # ls
  asg1612.dockerswarm        geerlingguy.git      geerlingguy.kibana       geerlingguy.postgresql
  geerlingguy.apache         geerlingguy.haproxy  geerlingguy.logstash    geerlingguy.redis
  geerlingguy.docker         geerlingguy.java     geerlingguy.mysql     geerlingguy.solr
  geerlingguy.elasticsearch  geerlingguy.jenkins  geerlingguy.pip       geerlingguy.sonar

