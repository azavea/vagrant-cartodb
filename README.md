# ansible-cartodb
Ansible role to build a multi-machine vagrant setup for CartoDB

Currently targets tag 3.12.2 on January 29, 2016

## Requirements

Before running `vagrant up`, install the following on your host machine:
  - vagrant 1.6+
  - ansible 1.8.4+

Then, clone the CartoDB repo to `../cartodb`:
```
git clone --recursive https://github.com/cartodb/cartodb ../cartodb
```
If this path is already in use for some reason, you can clone the repo elsewhere,
and update the path in the Vagrantfile.

Then, copy the template group vars file:
```
cp ansible/group_vars/all.example ansible/group_vars/all
```


Lastly, you'll need to add the following entries in your hosts file to point to the VM:
```
192.168.20.100 development.localhost.lan
192.168.20.100 localhost.lan
```

## Using the VM

Run `vagrant up`, wait for the machine to provision, then navigate to http://development.localhost.lan:3000/login
and login with:
 - Username: development
 - Password: cartodb


# Configuring the VM

All ansible configuration options can be set via variables in `ansible/group_vars/all`
