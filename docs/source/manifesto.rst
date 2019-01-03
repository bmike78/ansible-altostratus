.. _manifesto:

Manifesto: Project core values
==============================
This manifesto is an almost duplicate copy of the manifesto of `ARA`_

.. _ARA: https://github.com/openstack/ara/blob/40218b10f8f339fb75a4d79db7d21bd5785c2453/doc/source/manifesto.rst

Altostratus is an open source project that was created by Ansible users for Ansible
users.

Its purpose is to provide a way to simply and easily deploy cloud resources and
application deployment to your clouds.

Altostratus itself is composed of several components to achieve that purpose. The
project as well as those components adhere to some important core values.

This manifesto exists to explain the different core values incorporated in the
project's development and roadmap for users, contributors and developers alike.

1) Simplicity is fundamental
----------------------------

In the `Zen of Python`_, you'll find the following:

    **Simple is better than complex**

This is paramount to the project.
Altostratus should always be simple to install, simple to use and simple to understand.

Simplicity is also expressed in terms of configurability: Altostratus should come with
sane and working defaults out of the box.

It should be simple (but not required) to customize the behavior of Altostratus.

.. _Zen of Python: https://www.python.org/dev/peps/pep-0020/

2) Do one thing and do it well
------------------------------

The scope of the Altostratus project is narrow on purpose and is strongly aligned with
one of the values from the `UNIX philosophy`_:

    **Write programs that do one thing and do it well**

Altostratus is not trying to be a conglomeration of new code to deploy cloud resources,
but to use Ansible and Galaxy roles in a way that makes it easy to contribute, modify and
run application deployments using cloud resources.

A narrow project scope for Altostratus allows developers and users to focus on a
limited feature set in order to ensure each component is built and usable both
simply and optimally.

.. _UNIX philosophy: https://en.wikipedia.org/wiki/Unix_philosophy

3) Empower users to get their work done
---------------------------------------

This core value of the project is about being receptive to user feedback and
understanding what they need.

Altostratus should provide generic implementations to allow them to get their
work done while keeping in mind the two previous core values.

This warrants examples in order to have a common understanding of what this
means:

* Altostratus does not provide additional functionality beyond what is sent and
  made available by Ansible directly. Ansible upstream modules can be created,
  modified or fix that would then be made available to use in Altostratus.

* Additional cloud role playbooks can be developed and some methods and workflow
  can be reused.  Ansible galaxy or custom roles can be added to deploy additional
  applications or different competing application roles can be swapped out.

4) Don't require users to change their workflows
------------------------------------------------

Altostratus should never require users to change how they already use Ansible beyond
installing and configuring Ansible to use Altostratus.

Altostratus should be a drop-in, seamless and transparent addition to their workflows.

5) De-centralized, offline and standalone by default
----------------------------------------------------

It should never be required to run Ansible with Altostratus from one single, unique
and central location.

Users should be able to manage cloud resources and applications no matter where Ansible
runs, whether it is on their laptops, workstations, servers, virtual machines, etc.
