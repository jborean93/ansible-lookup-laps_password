# Ansible lookup laps_password integration tests

This repo is designed to document the install requirements of the lookup due to
the complexities of the `python-ldap` package as well as provide an easy way to
test the lookup against an actual environment.

Currently this lookup is going through the PR process at
https://github.com/ansible/ansible/pull/52012. Until this is merged into devel,
this should be checked out locally for these tests to run.


# Lookup requirements

The lookup itself requires the following Python libraries to be installed;

* [python-ldap](https://www.python-ldap.org/en/latest/)

The recommended way to install these packages is through PyPI with `pip` like
so;

```
pip install python-ldap
```

Installing from PyPI requires system packages to have already been installed on
the host before it will work. These packages differ based on the distribution
offer extra features like SASL/GSSAPI authentication. Please see below on what
system packages are required as well as an alternative way to install the above
packages for that distribution.

_Note: Installing these Python libraries using the system package instead of PyPI may result in an older package being installed that is unsupported. It is highly recommended you stick with PyPI to ensure you get the latest features._

## Package requirements

### RHEL/Centos

```
yum install cyrus-sasl-gssapi gcc openldap-devel python-devel
yum install gcc krb5-devel python-devel
```

The `cyrus-sasl-gssapi` package is not required to build python-ldap but it is
required if you wish to use `auth=gssapi` with this lookup.

You can optionally install the python libraries using yum instead of pip with;

```
yum install python-ldap
```

### Fedora

```
dnf install cyrus-sasl-gssapi gcc openldap-devel python3-devel
```

If running with Python 2, replace the `python3-devel` with just `python-devel`.
The `cyrus-sasl-gssapi` package is not required to build python-ldap but it is
required if you wish to use `auth=gssapi` with this lookup.

You can optionally install the Python libraries using dnf instead of pip with;

```
# Python 2
dnf install python-ldap

# Python 3
dnf install python3-ldap
```

### Debian/Ubuntu

```
apt-get install gcc libldap2-dev libsasl2-dev libsasl2-modules-gssapi-mit python3-dev
```

If running with Python 2, replace the `python3-dev` with just `python-dev`. The
`libsasl2-modules-gssapi-mit` is not required to build python-ldap but it is
required if you wish to use `auth=gssapi` with this lookup. It is highly
recommended that this is installed.

You can optionally install the Python libraries using apt instead of pip with;

```
# Python 2
apt-get install python-ldap

# Python 3
apt-get install python-ldap3
```

### Arch Linux

```
pacman -S cyrus-sasl-gssapi gcc libldap
```

The `cyrus-sasl-gssapi` package is not required to build python-ldap but it is
required if you wish to use `auth=gssapi` with this lookup.

The can optionally install the Python libraries using pacman instead of pip
with;

```
# Python 2
pacman -S python2-ldap

# Python 3
pacman -S python-ldap
```

### MacOS

MacOS comes with OpenLDAP already installed but it is an older version that
does not support all the features available like specifying a custom CACert
file for certificate validation. It is recommended that you install a newer
version of OpenLDAP with brew and reinstall python-ldap against this newer
version. To do this run;

```
brew install openldap

# Either clone to get the latest development or download the tar.gz release from
# https://github.com/python-ldap/python-ldap/releases
git clone https://github.com/python-ldap/python-ldap.git
cd python-ldap

# Add the following lines to the _ldap section in setup.cfg
# library_dirs = /usr/local/opt/openldap/lib /usr/lib /usr/local/lib
# include_dirs = /usr/local/opt/openldap/include /usr/local/include
sed -i "" "s%# library_dirs = .*%library_dirs = $(brew --prefix openldap)/lib /usr/lib /usr/local/lib%g" setup.cfg
sed -i "" "s%# include_dirs = .*%include_dirs = $(brew --prefix openldap)/include /usr/local/include%g" setup.cfg

python setup.py bdist_wheel
pip install dist/*.whl
```


# Testing requirements

To run these integration tests the following application must be installed

* [Vagrant](https://www.vagrantup.com/)
* [Virtualbox](https://www.virtualbox.org/)

Alongside these, you will also need to install the following Python libraries

* [ansible](https://pypi.org/project/ansible/)
* [pypsrp](https://pypi.org/project/pypsrp/)
* [pexpect](https://pypi.org/project/pexpect/)

These requirements are automatically installed on each of the test linux hosts
as part of the Vagrant provisioning stage. They are only required if you need
to run the tests locally.


# Setting up Environment

To setup the environment ensure that you have Vagrant, VirtualBox and Ansible
installed. Once that is done run the following commands;

```
ansible-galaxy install -r requirements.yml -p roles
vagrant up
```

This will take a while and will download a few Vagrant boxes.


# Running integration tests

You have the option of running the integration tests for each of the following
hosts;

* Centos - `vagrant ssh centos`
* Fedora - `vagrant ssh fedora`
* Ubuntu - `vagrant ssh ubuntu`
* Arch Linux - `vagrant ssh arch`
* Your own host - will require the pre-requisites and Kerberos to be correctly configured

To simple test against the pre-build hosts, run;

```
vagrant ssh <os distro>
cd ~/testing
ansible-playbook -i inventory.ini tests.yml -vv
```

If you are wanting to test against your current host, you will need to;

* Ensure DNS is set up correctly to talk to the domain host `dc01.ansible.laps` at `192.168.56.50`
* Installed all the pre-requisites for the `laps_password` plugin as well as any testing requirements mentioned above
* Ensure that you can retrieve a valid Kerberos token from the server

Once that is done you can run the integration tests by running
`ansible-playbook -i inventory.ini tests.yml`.


# Known Issues

There are currently a few known issues and incompatibilities. This section is
designed to track down these issues and post a fix if possible;

* You cannot use `auth=gssapi` with either `scheme=ldaps` or `start_tls=True`
    * Error is `Error was a <class 'ldap.SERVER_DOWN'>, original message: {u'info': 'Input/output error', 'errno': 5, 'desc': u\"Can't contact LDAP server\"}"`
* On MacOS, using the system OpenLDAP with python-ldap fails when specifying `cacert_file`
    * See the Lookup Requirements section for MacOS to find out how to install and use the latest OpenLDAP version
* On MacOS, using the latest OpenLDAP and python-ldap with Python 3.7 causes a freeze whenever TLS is used
