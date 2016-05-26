# vagrant-cartodb
Ansible role to build a vagrant-based development box for CartoDB


## Requirements

Before running `vagrant up`, do the following:


#### Install dependencies

On your host machine, install:
  - vagrant 1.6+
  - ansible 1.8.4+


#### Clone CartoDB

Clone the CartoDB repo to `../cartodb`:
```
git clone --recursive https://github.com/cartodb/cartodb ../cartodb
```
If this path is already in use for some reason, you can clone the repo elsewhere,
and set the `CARTODB_SRC_DIR` env variable before running `vagrant up`.


#### Copy configuration

Copy the template group vars file:
```
cp ansible/group_vars/all.example ansible/group_vars/all
```

#### Update hosts file

Add the following entries in your hosts file to point to the VM:
```
192.168.20.100 development.localhost.lan
192.168.20.100 localhost.lan
```

## Using the VM

Run `vagrant up`, wait for the machine to provision, then navigate to http://development.localhost.lan:3000/login
and login with:
 - Username: development
 - Password: cartodb


## Configuring the VM

All ansible configuration options can be set via variables in `ansible/group_vars/all`

If you would like to use a different tag of the cartodb repo, be sure to change the associated
versions of the sql api and windshaft in `ansible/group_vars/all`

Ensure you reprovision after making any changes to the ansible settings.
