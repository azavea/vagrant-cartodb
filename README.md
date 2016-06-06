# vagrant-cartodb
Ansible role to build a vagrant-based development box for CartoDB


## Getting started

Before running `vagrant up`, do the following:


#### Install dependencies

On your host machine, install:
  - vagrant 1.6+
  - ansible 1.9+


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

#### Services in the VM

The cartodb services (unicorn, resque, sql api, windshaft) are controlled via upstart. The service
names are:
- `cartodb_server` - Unicorn web worker processes
- `cartodb_resque_worker` - A single resque worker process (the VM runs two)
- `cartodb_windshaft` - Windshaft server
- `cartodb_sqlapi` - CartoDB SQL API server

Examples:
```
sudo service cartodb_server restart
sudo service cartodb_resque_worker restart WORKERNUM=1
sudo service cartodb_windshaft stop
```


## Configuring the VM

#### Vagrant configuration

The following ENV variables are available. They should be set before a `vagrant up`.
- `CARTODB_CPUS` - Number of CPUs to provide to the VM (default: `3`)
- `CARTODB_MEM` - Memory to provide to the VM (default: `4096`)
- `CARTODB_PRIVATE_IP` -  Private IP to provide to the vm (default: `192.168.20.100`)
- `CARTODB_SRC_DIR`: Relative directory path to a cartodb repository (default: `../cartodb`)


#### Ansible configuration

All ansible configuration options can be set via variables in `ansible/group_vars/all`

If you would like to use a different tag of the cartodb repo, be sure to change the associated
versions of the sql api and windshaft in `ansible/group_vars/all`

Ensure you reprovision after making any changes to the ansible settings.
