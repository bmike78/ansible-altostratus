.. _openstack:

Openstack
=========

Ansible playbook for launching instances and resources in Openstack.

Openstack Instance Inventory
----------------------------
Inventory for Openstack is done via `add_host`_ dynamically by using os_server_facts to gather information about the instances defined in the YAML file.

.. _add_host: https://docs.ansible.com/ansible/2.5/modules/add_host_module.html

Openstack Project Variables
---------------------------
Project variables are stored in group_vars::

  /openstack/<cloud>/<project>

Cloud
~~~~~
These are Openstack cloud variables.  These would be similar to what you would use in an openrc file to connect to Openstack via command line::
  
  cloud_url: https://openstack.example.com:5000/v3
  cloud_api_version: 3
  cloud_username: <project_user>
  cloud_password: <project_password>
  cloud_domain: EXAMPLE
  cloud_project: mike-project

Designate
~~~~~~~~~
Designate DNS is integrated and can be defined per project::

  cloud_designate: cloud.example.com
  cloud_timeout: 360
  cloud_email: admins@example.com

Keypair
~~~~~~~
This would be the keypair name you want to upload to the project (if not already there) and the location of the public key to upload::

  keypair_name: openstack-admin
  keypair_location: /root/.ssh/id_rsa.pub

Infoblox
~~~~~~~~
If using Infoblox for DNS creation, you would define these in the project::
  
  infoblox_server: <Grid IP>
  infoblox_username: <username>
  infoblox_password: <password>
  infoblox_api_version: 2.3.1
  infoblox_view: <view>
  infoblox_domain: openstack.example.com

Ideally, you could have a different domain or subdomain for each project or application and set permissions in Infoblox to offer separation between projects and DNS domains.

Openstack Instances Variables:
------------------------------
Instance and Application role variables are stored in:: 
  
  group_vars/openstack/<cloud>/<instances>/<instances/application name>

The name of the file could be either an instance, group of instances or an application name.

This is where security groups, security group rules, DNS (Designate, Infoblox, both or none), instance names, volume names, SSH key, flavor, network, instance state, application install and application roles::

  security_groups:
    - name: ssh
      state: present
      description: SSH Rule
    - name: ping
      state: present
      description: Ping Rule

  security_group_rules:
    - security_group: ssh
      state: present
      protocol: tcp
      port_range_min: 22
      port_range_max: 22
      remote_ip_prefix: 0.0.0.0/0
    - security_group: ping
      state: present
      protocol: icmp
      remote_ip_prefix: 0.0.0.0/0

  designate: true
  infoblox: true

  dns:
    - name: "{{ instance_name }}"
      zone: "{{ cloud_project }}.{{ cloud_designate }}."
      description: "Record for {{ instance_name }}"
      recordset_type: A
      ttl: 100

  instance_names:
    - instance_name: mlab90app1
      #instance_state: absent
      instance_state: present
      instance_key: openstack-admin
      instance_security_groups: ping,ssh
      instance_image_regex: centos-7-current
      instance_flavor: m1.large
      instance_boot_from_volume: false
      instance_network: mike-project_private
      #instance_network: lab-shared-783_network
      instance_terminate_volume: true
      instance_auto_ip: true
      #instance_auto_ip: false
      instance_lock: true
      instance_volumes:
      - name: mlab90app1-vol1
        size: 10
        type: volumes_ceph
        device: /dev/vdb
      - name: mlab90app1-vol2
        size: 15
        type: volumes_ceph
        device: /dev/vdc
      instance_roles:
        - role: apache
        - role: ansible
        - role: disk-performance
        - role: docker
        - role: elasticsearch
        - role: git
        - role: haproxy
        - role: java
        - role: jenkins
        - role: kibana
        - role: logstash
        - role: mysql
        - role: postgresql
        - role: redis
        - role: solr
        - role: sonar
      app_install: true

  ## Enable Application Install (comment out to disable)
  app_install: true

Features
--------
.. list-table:: Openstack
   :widths: 50 25 25
   :header-rows: 1

   * - Feature
     - Role
     - Variables
   * - Create/Delete instance in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Create/Delete multiple instances in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Modify instance in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Modify multiple instances in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Create/Delete/Attach volumes to instance
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Create/Delete/Attach volumes to multiple instances
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Uses server name specified in yaml
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Uses userdata for post install
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/instances/<name>
   * - Create/Deletes Designate DNS zone in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/zone/<name>
   * - Create/Deletes Swift container in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/container/<name>
   * - Create/Deletes Heat stack in openstack
     - roles/openstack/instance
     - group_vars/openstack/<cloud>/stack/<name>

Playbook Files
--------------
::

  instance/tasks/main.yml - Main role file
    
  instance/tasks/keypair.yml - Add SSH keypair to project and instance
  instance/tasks/security_group.yml - Add security groups to instance
  instance/tasks/delete_multiple.yml - Delete instance workflow variables for each instance (instance_state=absent)
  instance/tasks/delete.yml - Delete instance workflow for single instance (instance_state=absent)
  instance/tasks/unlock.yml - Unlock Instance
  instance/tasks/facts.yml - Gather facts about instance
  instance/tasks/instance.yml - Add/Delete/Modify instance
  instance/tasks/volumes.yml - Add volumes to instance
  instance/tasks/designate.yml - Add/Delete/Modify DNS Record for instance in Designate
  instance/tasks/infoblox.yml - Add/Delete/Modify DNS Record in for instance Infoblox
  instance/tasks/create_multiple.yml - Create instance workflow variables for each instance (instance_state=present)
  instance/tasks/create.yml - Create instance workflow for single instance (instance_state=present)
  instance/tasks/image.yml - Gather latest image (based on instance_image_regex)
  instance/tasks/unlock.yml - Unlock Instance
  instance/tasks/instance.yml - Add/Delete/Modify instance
  instance/tasks/volumes.yml - Add volumes to instance
  instance/tasks/lock.yml - Lock Instance
  instance/tasks/facts.yml - Gather facts about instance
  instance/tasks/designate.yml - Add/Delete/Modify DNS Record for instance in Designate
  instance/tasks/infoblox.yml - Add/Delete/Modify DNS Record in for instance Infoblox
  instance/tasks/app_install.yml - Application Install Add Roles

  container/tasks/container.yml - Add/Delete/Modify Swift Container for project

  stack/tasks/stack.yml - Add/Delete/Modify Heat Stack for project
  
  zone/tasks/zone.yml - Add/Delete/Modify DNS Zone in Openstack for project

Example Playbook - Openstack instance creation and application installation:
----------------------------------------------------------------------------
Create openstack instance mlab90app1 on openstack.example.com and install Apache and MySQL::

  (venv-ansible-altostratus)
  server1:/etc/ansible/ansible-altostratus
  # ansible-playbook os_instance-mlab90app1.yml
  PLAY [configuration of altostratus Openstack instances]  *********************************************************************************************************************************************

  TASK [openstack/instance : Upload keypair openstack-admin] ******************************************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : Create security groups for mike-project]   *********************************************************************************************************************************
  ok: [localhost] => (item={u'state': u'present', u'name': u'ssh', u'description': u'SSH Rule'})
  ok: [localhost] => (item={u'state': u'present', u'name': u'ping', u'description': u'Ping Rule'})

  TASK [openstack/instance : Create TCP/UDP security groups rules for mike-project] *******************************************************************************************************************
  ok: [localhost] => (item={u'protocol': u'tcp', u'remote_ip_prefix': u'0.0.0.0/0', u'port_range_max': 22, u'state': u'present', u'port_range_min': 22, u'security_group': u'ssh'})
  skipping: [localhost] => (item={u'security_group': u'ping', u'remote_ip_prefix': u'0.0.0.0/0', u'state': u'present', u'protocol': u'icmp'})

  TASK [openstack/instance : Create ICMP security groups rules for mike-project] **********************************************************************************************************************
  skipping: [localhost] => (item={u'protocol': u'tcp', u'remote_ip_prefix': u'0.0.0.0/0', u'port_range_max': 22, u'state': u'present', u'port_range_min': 22, u'security_group': u'ssh'})
  ok: [localhost] => (item={u'security_group': u'ping', u'remote_ip_prefix': u'0.0.0.0/0', u'state': u'present', u'protocol': u'icmp'})

  TASK [openstack/instance : pass variables into include for instance deletion] ***********************************************************************************************************************
  skipping: [localhost] => (item={u'instance_terminate_volume': True, u'app_install': True, u'instance_key': u'openstack-admin', u'instance_image_regex': u'centos-7-current', u'instance_boot_from_volume': False, u'instance_security_groups': u'ping,ssh', u'instance_name': u'mlab90app1', u'instance_state': u'present', u'instance_network': u'mike-project_private', u'instance_volumes': [{u'device': u'/dev/vdb', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol1', u'size': 10}, {u'device': u'/dev/vdc', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol2', u'size': 15}], u'instance_roles': [{u'role': u'apache'}, {u'role': u'mysql'}], u'instance_flavor': u'm1.large', u'instance_lock': True, u'instance_auto_ip': True})

  TASK [openstack/instance : pass variables into include for instance creation] ***********************************************************************************************************************
  included: /etc/ansible//ansible-altostratus/roles/openstack/instance/tasks/create.yml for localhost

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/openstack/ansible-altostratus/roles/openstack/instance/tasks/image.yml for localhost

  TASK [openstack/instance : Gathering latest centos-7-current image] *********************************************************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : Set fact for instance_image to centos-7-current-1538352316] **************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/unlock.yml for localhost

  TASK [openstack/instance : unlock instance] *********************************************************************************************************************************************************
  fatal: [localhost]: FAILED! => {"changed": false, "msg": "Could not find server None"}
  ...ignoring

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/instance.yml for localhost

  TASK [openstack/instance : Setting managed instance mlab90app1 to present with image centos-7-current-1538352316] ***********************************************************************************
  skipping: [localhost]

  TASK [openstack/instance : Setting instance mlab90app1 to present with image centos-7-current-1538352316] *******************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/volumes.yml for localhost

  TASK [openstack/instance : Setting volumes for mlab90app1 to present] *******************************************************************************************************************************
  changed: [localhost] => (item={u'device': u'/dev/vdb', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol1', u'size': 10})
  changed: [localhost] => (item={u'device': u'/dev/vdc', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol2', u'size': 15})

  TASK [openstack/instance : Attach volumes for mlab90app1] ******************************************************************************************************************************************* 
  changed: [localhost] => (item={u'device': u'/dev/vdb', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol1', u'size': 10})
  changed: [localhost] => (item={u'device': u'/dev/vdc', u'type': u'volumes_ceph', u'name': u'mlab90app1-vol2', u'size': 15})

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/lock.yml for localhost

  TASK [openstack/instance : lock instance] ***********************************************************************************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/facts.yml for localhost

  TASK [openstack/instance : facts instance] **********************************************************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : facts instance output] ***************************************************************************************************************************************************
  ok: [localhost] => {
    "openstack_servers": [
        {
            "OS-DCF:diskConfig": "MANUAL",
            "OS-EXT-AZ:availability_zone": "nova",
            "OS-EXT-STS:power_state": 1,
            "OS-EXT-STS:task_state": null,
            "OS-EXT-STS:vm_state": "active",
            "OS-SRV-USG:launched_at": "2018-10-10T13:36:50.000000",
            "OS-SRV-USG:terminated_at": null,
            "accessIPv4": "192.168.84.52",
            "accessIPv6": "",
            "addresses": {
                "mike-project_private": [
                    {
                        "OS-EXT-IPS-MAC:mac_addr": "fa:16:3e:be:7d:ba",
                        "OS-EXT-IPS:type": "fixed",
                        "addr": "10.0.0.21",
                        "version": 4
                    },
                    {
                        "OS-EXT-IPS-MAC:mac_addr": "fa:16:3e:be:7d:ba",
                        "OS-EXT-IPS:type": "floating",
                        "addr": "192.168.84.52",
                        "version": 4
                    }
                ]
            },
            "adminPass": null,
            "az": "nova",
            "cloud": "",
            "config_drive": "",
            "created": "2018-10-10T13:36:23Z",
            "created_at": "2018-10-10T13:36:23Z",
            "disk_config": "MANUAL",
            "flavor": {
                "id": "4"
            },
            "has_config_drive": false,
            "hostId": "1308e64eaa48f7beb44103dfdc7a8c0e3d09ace469f35af08e7bd28d",
            "host_id": "1308e64eaa48f7beb44103dfdc7a8c0e3d09ace469f35af08e7bd28d",
            "id": "095a648a-bf77-47af-b9f7-4538c3f756eb",
            "image": {
                "id": "3fe3e4fa-e699-4ba6-bac2-a27a1b09db5e"
            },
            "interface_ip": "192.168.84.52",
            "key_name": "openstack-admin",
            "launched_at": "2018-10-10T13:36:50.000000",
            "location": {
                "cloud": "",
                "project": {
                    "domain_id": null,
                    "domain_name": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
                    "name": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "region_name": "",
                "zone": "nova"
            },
            "metadata": {},
            "name": "mlab90app1",
            "networks": {},
            "os-extended-volumes:volumes_attached": [
                {
                    "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                },
                {
                    "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                }
            ],
            "power_state": 1,
            "private_v4": "10.0.0.21",
            "progress": 0,
            "project_id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
            "properties": {
                "OS-DCF:diskConfig": "MANUAL",
                "OS-EXT-AZ:availability_zone": "nova",
                "OS-EXT-STS:power_state": 1,
                "OS-EXT-STS:task_state": null,
                "OS-EXT-STS:vm_state": "active",
                "OS-SRV-USG:launched_at": "2018-10-10T13:36:50.000000",
                "OS-SRV-USG:terminated_at": null,
                "os-extended-volumes:volumes_attached": [
                    {
                        "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                    },
                    {
                        "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                    }
                ]
            },
            "public_v4": "192.168.84.52",
            "public_v6": "",
            "region": "",
            "security_groups": [
                {
                    "name": "ssh"
                },
                {
                    "name": "ping"
                }
            ],
            "status": "ACTIVE",
            "task_state": null,
            "tenant_id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
            "terminated_at": null,
            "updated": "2018-10-10T13:38:37Z",
            "user_id": "0019df5ac0f473d34d8cd59ae7d18fda58f474b199c26238e9ee892a8486dec9",
            "vm_state": "active",
            "volumes": [
                {
                    "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                },
                {
                    "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                }
            ]
        }
    ]
  }

  TASK [openstack/instance : register instance_ip for Floating IP] ************************************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : register instance_ip for Shared IP] **************************************************************************************************************************************
  skipping: [localhost]

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/designate.yml for localhost

  TASK [openstack/instance : Create or Delete Designate DNS entry for mlab90app1.mike-project.cloud.example.com using 192.168.84.52] ********************************************************************
  changed: [localhost] => (item={u'name': u'mlab90app1', u'recordset_type': u'A', u'description': u'Record for mlab90app1', u'zone': u'mike-project.cloud.example.com.', u'ttl': 100})

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/infoblox.yml for localhost

  TASK [openstack/instance : Create or Delete Infoblox DNS entry for mlab90app1.infoblox.example.com using 192.168.84.52] *************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/app_install.yml for localhost

  TASK [openstack/instance : Wait for SSH on the Instance] ********************************************************************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : Add Instance to Ansible Inventory and Groups for Application Roles] ******************************************************************************************************
  changed: [localhost] => (item={u'role': u'apache'})
  changed: [localhost] => (item={u'role': u'mysql'})

  TASK [openstack/instance : pass variables into include for instance creation] ***********************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/app_install_test.yml for localhost

  TASK [openstack/instance : include] *****************************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/openstack/instance/tasks/facts.yml for localhost

  TASK [openstack/instance : facts instance] **********************************************************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : facts instance output] ***************************************************************************************************************************************************
  ok: [localhost] => {
    "openstack_servers": [
        {
            "OS-DCF:diskConfig": "MANUAL",
            "OS-EXT-AZ:availability_zone": "nova",
            "OS-EXT-STS:power_state": 1,
            "OS-EXT-STS:task_state": null,
            "OS-EXT-STS:vm_state": "active",
            "OS-SRV-USG:launched_at": "2018-10-10T13:36:50.000000",
            "OS-SRV-USG:terminated_at": null,
            "accessIPv4": "192.168.84.52",
            "accessIPv6": "",
            "addresses": {
                "mike-project_private": [
                    {
                        "OS-EXT-IPS-MAC:mac_addr": "fa:16:3e:be:7d:ba",
                        "OS-EXT-IPS:type": "fixed",
                        "addr": "10.0.0.21",
                        "version": 4
                    },
                    {
                        "OS-EXT-IPS-MAC:mac_addr": "fa:16:3e:be:7d:ba",
                        "OS-EXT-IPS:type": "floating",
                        "addr": "192.168.84.52",
                        "version": 4
                    }
                ]
            },
            "adminPass": null,
            "az": "nova",
            "cloud": "",
            "config_drive": "",
            "created": "2018-10-10T13:36:23Z",
            "created_at": "2018-10-10T13:36:23Z",
            "disk_config": "MANUAL",
            "flavor": {
                "id": "4"
            },
            "has_config_drive": false,
            "hostId": "1308e64eaa48f7beb44103dfdc7a8c0e3d09ace469f35af08e7bd28d",
            "host_id": "1308e64eaa48f7beb44103dfdc7a8c0e3d09ace469f35af08e7bd28d",
            "id": "095a648a-bf77-47af-b9f7-4538c3f756eb",
            "image": {
                "id": "3fe3e4fa-e699-4ba6-bac2-a27a1b09db5e"
            },
            "interface_ip": "192.168.84.52",
            "key_name": "openstack-admin",
            "launched_at": "2018-10-10T13:36:50.000000",
            "location": {
                "cloud": "",
                "project": {
                    "domain_id": null,
                    "domain_name": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
                    "id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
                    "name": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER"
                },
                "region_name": "",
                "zone": "nova"
            },
            "metadata": {},
            "name": "mlab90app1",
            "networks": {},
            "os-extended-volumes:volumes_attached": [
                {
                    "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                },
                {
                    "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                }
            ],
            "power_state": 1,
            "private_v4": "10.0.0.21",
            "progress": 0,
            "project_id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
            "properties": {
                "OS-DCF:diskConfig": "MANUAL",
                "OS-EXT-AZ:availability_zone": "nova",
                "OS-EXT-STS:power_state": 1,
                "OS-EXT-STS:task_state": null,
                "OS-EXT-STS:vm_state": "active",
                "OS-SRV-USG:launched_at": "2018-10-10T13:36:50.000000",
                "OS-SRV-USG:terminated_at": null,
                "os-extended-volumes:volumes_attached": [
                    {
                        "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                    },
                    {
                        "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                    }
                ]
            },
            "public_v4": "192.168.84.52",
            "public_v6": "",
            "region": "",
            "security_groups": [
                {
                    "name": "ssh"
                },
                {
                    "name": "ping"
                }
            ],
            "status": "ACTIVE",
            "task_state": null,
            "tenant_id": "4b5ed4f81e8a44dfb78614d1cf7a6663",
            "terminated_at": null,
            "updated": "2018-10-10T13:38:37Z",
            "user_id": "0019df5ac0f473d34d8cd59ae7d18fda58f474b199c26238e9ee892a8486dec9",
            "vm_state": "active",
            "volumes": [
                {
                    "id": "9461c748-d1aa-4a65-ba8a-41292712a0d2"
                },
                {
                    "id": "9d4b4dc0-a44f-4855-9be2-9c6f7503d53e"
                }
            ]
        }
    ]
  }

  TASK [openstack/instance : register instance_ip for Floating IP] ************************************************************************************************************************************
  ok: [localhost]

  TASK [openstack/instance : register instance_ip for Shared IP] **************************************************************************************************************************************
  skipping: [localhost]

  TASK [openstack/instance : Wait for SSH on the Instance] ********************************************************************************************************************************************
  changed: [localhost]

  TASK [openstack/instance : Add Instance to Ansible Inventory and Groups for Application Roles] ******************************************************************************************************
  changed: [localhost] => (item={u'role': u'apache'})
  changed: [localhost] => (item={u'role': u'mysql'})
 [WARNING]: Could not match supplied host pattern, ignoring: pip


  PLAY [configuration of altostratus apps - geerlingguy.pip] ******************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: ansible


  PLAY [configuration of altostratus apps - ansible] **************************************************************************************************************************************************
  skipping: no hosts matched

  PLAY [configuration of altostratus apps - geerlingguy.apache] ***************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : Include OS-specific variables.] ***********************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : Include variables for Amazon Linux.] ******************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : Define apache_packages.] ******************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : include_tasks] ****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.apache/tasks/setup-RedHat.yml for mlab90app1

  TASK [galaxy/geerlingguy.apache : Ensure Apache is installed on RHEL.] ******************************************************************************************************************************
  changed: [mlab90app1] => (item=[u'httpd', u'httpd-devel', u'mod_ssl', u'openssh'])

  TASK [galaxy/geerlingguy.apache : Get installed version of Apache.] *********************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : Create apache_version variable.] **********************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : include_vars] *****************************************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : include_vars] *****************************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : include_tasks] ****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.apache/tasks/configure-RedHat.yml for mlab90app1

  TASK [galaxy/geerlingguy.apache : Configure Apache.] ************************************************************************************************************************************************
  ok: [mlab90app1] => (item={u'regexp': u'^Listen ', u'line': u'Listen 80'})

  TASK [galaxy/geerlingguy.apache : Check whether certificates defined in vhosts exist.] **************************************************************************************************************

  TASK [galaxy/geerlingguy.apache : Add apache vhosts configuration.] *********************************************************************************************************************************
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.apache : Ensure Apache has selected state and enabled on boot.] ************************************************************************************************************
  changed: [mlab90app1]

  RUNNING HANDLER [galaxy/geerlingguy.apache : restart apache] ****************************************************************************************************************************************
  changed: [mlab90app1]
 [WARNING]: Could not match supplied host pattern, ignoring: elasticsearch


  PLAY [configuration of altostratus apps - geerlingguy.elasticsearch] ********************************************************************************************************************************
  skipping: no hosts matched

  PLAY [configuration of altostratus apps - geerlingguy.mysql] ****************************************************************************************************************************************

  TASK [Gathering Facts] ******************************************************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/variables.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Include OS-specific variables.] ************************************************************************************************************************************

  TASK [galaxy/geerlingguy.mysql : Include OS-specific variables (RedHat).] ***************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_packages.] ********************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_daemon.] **********************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_slow_query_log_file.] *********************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_log_error.] *******************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_syslog_tag.] ******************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_pid_file.] ********************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_config_file.] *****************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_config_include_dir.] **********************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_socket.] **********************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Define mysql_supports_innodb_large_prefix.] ************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/setup-RedHat.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Ensure MySQL packages are installed.] ******************************************************************************************************************************
  changed: [mlab90app1] => (item=[u'mariadb', u'mariadb-server', u'mariadb-libs', u'MySQL-python', u'perl-DBD-MySQL'])

  TASK [galaxy/geerlingguy.mysql : Ensure MySQL Python libraries are installed.] **********************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Check if MySQL packages were installed.] ***************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/configure.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Get MySQL version.] ************************************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Copy my.cnf global MySQL configuration.] ***************************************************************************************************************************
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Verify mysql include directory exists.] ****************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Copy my.cnf override files into include directory.] ****************************************************************************************************************

  TASK [galaxy/geerlingguy.mysql : Create slow query log file (if configured).] ***********************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Create datadir if it does not exist] *******************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Set ownership on slow query log file (if configured).] *************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Create error log file (if configured).] ****************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Set ownership on error log file (if configured).] ******************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Ensure MySQL is started and enabled on boot.] **********************************************************************************************************************
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/secure-installation.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Ensure default user is present.] ***********************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Copy user-my.cnf file with password credentials.] ******************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Disallow root login remotely] **************************************************************************************************************************************
  ok: [mlab90app1] => (item=DELETE FROM mysql.user WHERE User='root' AND Host NOT IN ('localhost', '127.0.0.1', '::1'))

  TASK [galaxy/geerlingguy.mysql : Get list of hosts for the root user.] ******************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Update MySQL root password for localhost root account (5.7.x).] ****************************************************************************************************
  skipping: [mlab90app1] => (item=127.0.0.1)
  skipping: [mlab90app1] => (item=::1)
  skipping: [mlab90app1] => (item=localhost)

  TASK [galaxy/geerlingguy.mysql : Update MySQL root password for localhost root account (< 5.7.x).] **************************************************************************************************
  changed: [mlab90app1] => (item=127.0.0.1)
  changed: [mlab90app1] => (item=::1)
  changed: [mlab90app1] => (item=localhost)

  TASK [galaxy/geerlingguy.mysql : Copy .my.cnf file with root password credentials.] *****************************************************************************************************************
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Get list of hosts for the anonymous user.] *************************************************************************************************************************
  ok: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Remove anonymous MySQL users.] *************************************************************************************************************************************
  changed: [mlab90app1] => (item=localhost)
  changed: [mlab90app1] => (item=mlab90app1.openstacklocal)

  TASK [galaxy/geerlingguy.mysql : Remove MySQL test database.] ***************************************************************************************************************************************
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/databases.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Ensure MySQL databases are present.] *******************************************************************************************************************************
  changed: [mlab90app1] => (item={u'collation': u'latin1_general_ci', u'name': u'example_db', u'encoding': u'latin1'})

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/users.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Ensure MySQL users are present.] ***********************************************************************************************************************************
  changed: [mlab90app1] => (item=None)
  changed: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : include_tasks] *****************************************************************************************************************************************************
  included: /etc/ansible/ansible-altostratus/roles/galaxy/geerlingguy.mysql/tasks/replication.yml for mlab90app1

  TASK [galaxy/geerlingguy.mysql : Ensure replication user exists on master.] *************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Check slave replication status.] ***********************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Check master replication status.] **********************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Configure replication on the slave.] *******************************************************************************************************************************
  skipping: [mlab90app1]

  TASK [galaxy/geerlingguy.mysql : Start replication.] ************************************************************************************************************************************************
  skipping: [mlab90app1]

  RUNNING HANDLER [galaxy/geerlingguy.mysql : restart mysql] ******************************************************************************************************************************************
 [WARNING]: Ignoring "sleep" as it is not used in "systemd"

  changed: [mlab90app1]
 [WARNING]: Could not match supplied host pattern, ignoring: postgresql


  PLAY [configuration of altostratus apps - geerlingguy.postgresql] ***********************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: disk-performance


  PLAY [configuration of altostratus apps - disk-performance] *****************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: docker


  PLAY [configuration of altostratus apps - geerlingguy.docker] ***************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: git


  PLAY [configuration of altostratus apps - geerlingguy.git] ******************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: haproxy


  PLAY [configuration of altostratus apps - geerlingguy.haproxy] **************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: icinga2-ansible-no-ui


  PLAY [configuration of altostratus apps - icinga2-server] *******************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: java


  PLAY [configuration of altostratus apps - geerlingguy.java] *****************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: jenkins


  PLAY [configuration of altostratus apps - geerlingguy.jenkins] **************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: kibana


  PLAY [configuration of altostratus apps - geerlingguy.kibana] ***************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: logstash


  PLAY [configuration of altostratus apps - geerlingguy.logstash] *************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: redis


  PLAY [configuration of altostratus apps - geerlingguy.redis] ****************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: solr


  PLAY [configuration of altostratus apps - geerlingguy.solr] *****************************************************************************************************************************************
  skipping: no hosts matched
 [WARNING]: Could not match supplied host pattern, ignoring: sonar


  PLAY [configuration of altostratus apps - geerlingguy.sonar] ****************************************************************************************************************************************
  skipping: no hosts matched

  PLAY RECAP ******************************************************************************************************************************************************************************************
  localhost                  : ok=35   changed=11   unreachable=0    failed=0
  mlab90app1                 : ok=51   changed=14   unreachable=0    failed=0
