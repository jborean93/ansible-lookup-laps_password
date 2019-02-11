# Ansible lookup laps_password integration tests

This is a repo that can be used to setup an environment to test out the
`laps_password` Ansible lookup. It also contains a playbook that runs a bunch
of integration tests against the lookup plugin.


# Requirements

To run these integration tests the following application must be installed

* [OpenLDAP](https://www.openldap.org/)
* [Vagrant](https://www.vagrantup.com/)
* [Virtualbox](https://www.virtualbox.org/)

Alongside these, you will also need to install the following Python libraries

* [ansible](https://pypi.org/project/ansible/)
* [pypsrp](https://pypi.org/project/pypsrp/)
* [python-ldap](https://www.python-ldap.org/en/latest/)
* [gssapi](https://pypi.org/project/gssapi/)

If running on MacOS it is recommended to install a newer version of OpenLDAP
with brew and then reinstall `python-ldap` against this newer version. To do
this run

```
brew install openldap

git clone https://github.com/python-ldap/python-ldap.git
cd python-ldap

# Add the following lines to the _ldap section in setup.cfg
# library_dirs = /usr/local/opt/openldap/lib /usr/lib /usr/lib64 /usr/local/lib /usr/local/lib64
# include_dirs = /usr/local/opt/openldap/include /usr/include /usr/include/sasl /usr/local/include /usr/local/include/sasl
python setup.py bdist_wheel
pip install dist/<python-*>.whl
```

Some features will work without updated OpenLDAP but other features like
custom CA Certs will fail.


# Setting up Environment

To setup the environment ensure you have met all the pre-requisites, once that
is done run the following commands;

```
ansible-galaxy install -r requirements.yml -p roles
vagrant up
```


# Running integration tests

Now that the host has been set up the next step is to setup DNS correctly on
the current host so that it can talk to the domain controller and retrieve
Kerberos tokens.

Once that is done you can run the integration tests by running
`ansible-playbook -i inventory.ini tests.yml`.
