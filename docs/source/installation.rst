.. _installation:

Installing Altostratus
======================

Installing Altostratus is easy.

If you have git cloned this repo and followed the advice to create a python virtual environment, then it is mostly installed.
::
  # git clone https://github.com/bmike78/ansible-altostratus.git
  # cd ansible-altostratus
  # virtualenv venv-ansible-altostratus
  New python executable in venv-ansible-altostratus/bin/python
  # source venv-ansible-altostratus/bin/activate
  (venv-ansible-altostratus)
  # pip install setuptools --upgrade
  # pip install -r requirements.txt

Galaxy Roles
~~~~~~~~~~~~
To install, reinstall or add Galaxy roles, add them to requirements.yml, follow the :ref:`documentation <applications>` for applications.

