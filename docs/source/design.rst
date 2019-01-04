.. _design:

Design
===

Why Was Altostratus Designed?
---------------------
`Altostratus`_ is designed to utilize `Ansible`_ playbook Galaxy and custom roles to interact with cloud resources, such as `Openstack`_ and `CloudFoundry`_ to manage resources in multiple clouds.  As an IT professional who manages several Openstack clouds, is a CloudFoundry consumer and potential consumer of other clouds such as AWS, Azure and GCP, I wanted to come up with a way to manage these resources easily and allow for cloud portability if my workload needs to shift from one cloud to the other or if I need to run a playbook that interacts with multiple cloud providers.

- Ansible should be easy enough to use, but most examples I found were of very specific examples and this project involves taking all those examples together to go from instance creation to application deployment.
- Creation of instances should work the same no matter what provider is used.  This is an easy thing to say until every cloud provider gives you a different way of interacting with them.  One is easy, many is hard.
- Ansible Galaxy roles are great, but some need to be customized or there may be a requirement not to share custom roles back with the community. 
- Sometimes in the middle of an existing deployment, settings, configuration or individual roles need to be changed and so this allows for these kind of support activities to happen.
- There are orchestration tools like Openstack Heat or AWS CloudFormation that accomplish similar things to Ansible, but are not cloud agnostic.
- I found that my end users were enjoying the ease of use of Openstack Horizon and automation using Jenkins, but true automation end to end often involved using multiple tools chained together, worrying about configuring outside dependencies like DNS, load balancers and monitoring, whereas using just Ansible to do the bulk of the automation end to end makes more sense.
- Many of my teams are starting to use Ansible or have their own automation efforts, so this project is an attempt to utilize one tool for these efforts.

.. _altostratus: https://github.com/bmike78/ansible-altostratus
.. _ansible: https://www.ansible.com/
.. _openstack: https://www.openstack.org/
.. _cloudfoundry: https://www.cloudfoundry.org/

How is Altostratus Designed?
---------------------
- `Altostratus`_ uses Ansible at its core to create Openstack instance or instances.  The Ansible os_ modules work very well to orchestrate most things in an Openstack cloud, so all of these operations can be automated as part of a playbook instead of coding a bunch of custom code against the Openstack APIs.
- Once Openstack resources are created and idempotent, then the playbook will move on to application install based on roles that are added to the instance's yml file.
- A declaration of roles per instance is done in the yml file as well, so a multiple instance deployment stack like Docker Swarm or Kubernetes is also possible.
- Integration with Designate and Infoblox allow for DNS records to be created, deleted or modified as well.
- Because Ansible is idempotent, changes can be made to creation, modification and deletion of instances or additional roles can be added to existing instances to take care of the resources in their lifecycle.
- group_vars is used to store variables for each Openstack cloud, CloudFoundry app or deployed application allowing for a whole organization to contribute to the project and be utilized by multiple teams.
- Usage of GitHub can be used for CloudFoundry to check out code and push or can be pushed locally to CloudFoundry.  This flexibility allows for end users to test out new features or smoke test their deployment to CloudFoundry before commiting the code up to GitHub for a production deployment.
- This project can be opened up for other clouds such as AWS, Azure, GCP, Kubernetes, Openshift, or any other `cloud modules`_ that Ansible has modules for.  I hope that others will contribute provisioning roles for using these clouds. 

.. _cloud modules: https://docs.ansible.com/ansible/latest/modules/list_of_cloud_modules.html  

Where does Altostraus Fit in My Automation?
---------------------
- `Altostratus`_ serves as a provisioner for cloud resources and application deployments.
- The goal is not to displace these existing tools, but to make the ease of deploying cloud resources and application deployment easier.
- You can run it from the command line using ansible-playbook or spawn it using any number of tools like Jenkins, Tower, Rundeck, etc...

.. image:: _static/altostratus-diagram.JPG

- 
