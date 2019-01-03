.. _cloudfoundry:

CloudFoundry
============

Ansible playbook for launching applications in CloudFoundry.

CloudFoundry Variables
----------------------
Most variables are stored in::

  group_vars/cloudfoundry/<cloud>/<org+space>.yml
  group_vars/cloudfoundry/<cloud>/<app_name>

The same app_name can be used for multiple orgs and spaces.

App State
~~~~~~~~~
The app_state can either be "present", "absent" or "redeploy".  A redeploy will simply delete from CloudFoundry and add it back.

App Manifest
~~~~~~~~~~~~
A `manifest`_ can be included as part of the deployment.  The manifest resides in the root folder of the github repo or local repo.

.. _manifest: https://docs.cloudfoundry.org/devguide/deploy-apps/manifest.html

Files
~~~~~
Need to drop some files for configuration or credentials as part of the deployment?  Files are located in::

  roles/cloudfoundry/files/<app_name>/<files or directory of files>

Other App Properties
~~~~~~~~~~~~~~~~~~~~
::

  app_instances - Number of instances to run in CloudFoundry for the deployment.
  app_memory - Amount of memory to give the instances in CloudFoundry.
  app_route - Route for application
  app_hostname - Hostname for appplication
  app_domain - Domain for application

Deployments
-----------

Git Deployment
~~~~~~~~~~~~~~
Deployments of CF can be done via git::

  git_repo: https://github.com/bmike78/python-flask-cf-test.git
  git_dir: /tmp/git
  app_name: flask-test
  app_manifest: flask-test-manifest.xml
  #app_state: present
  #app_state: absent
  app_state: redeploy

Local Repo Deployment
~~~~~~~~~~~~~~~~~~~~~
Deployments can also be done locally::

  git_repo: local
  git_dir: /root/cloudfoundry
  app_name: localapp
  #app_state: present
  #app_state: absent
  app_state: redeploy

Repo Cleanup
~~~~~~~~~~~~
By default if using a git repo that is not local, the files will be deleted from the git_dir after the deployment is run.

Features
--------
.. list-table:: CloudFoundry 
   :widths: 50 25 25
   :header-rows: 1

   * - Feature
     - Role
     - Variables
   * - Login to CF environment and space
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<org+space>.yml
   * - Deployment of CF app from local repo
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<app>
   * - Deployment of CF app from github repo
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<app>
   * - Deployment of CF app with manifest.yml
     - roles/cloudfoundry
     - repo directory (either local or pulled from git repo)
   * - Create Domain
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<app>
   * - Create Route (hostname)
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<app>
   * - Map Route
     - roles/cloudfoundry
     - group_vars/cloudfoundry/<cloud>/<project>/<app>

Playbook Files
--------------
::

  cleanup.yml - Clean up local git repo of downloaded files
  config.yml - Drop config files into repo prior to CF deploy (passwords, configs)
  delete.yml - Delete CF app
  domain.yml - Create CF domain after CF push
  git.yml - Download app from github prior to CF push
  hostname.yml - Create CF hostname after CF push
  login.yml - Login to CF API project and space
  manifest.yml - Push CF app using manifest file
  push.yml - Push CF app without manifest file

Example Playbooks
-----------------

Local Deployment from local
~~~~~~~~~~~~~~~~~~~~~~~~~~~

Remote Deployment from git repo
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
