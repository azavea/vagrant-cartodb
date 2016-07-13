# vagrant-cartodb
Ansible role to build a vagrant-based development box for CartoDB


## Getting started

Before running `vagrant up`, do the following:


#### Install dependencies

On your host machine, install:
  - vagrant 1.6+
  - ansible 2.0+


#### Clone CartoDB

Clone the CartoDB repo to `../cartodb`:
```
git clone --recursive https://github.com/cartodb/cartodb ../cartodb
```
If this path is already in use for some reason, you can clone the repo elsewhere,
and set the `CARTODB_SRC_DIR` env variable before running `vagrant up`.

##### Note to OS X users
The stock version of git for OS X El Capitan, `2.7.4 (Apple Git-66)`, behaves differently
when recursively cloning repositories than other versions of git. This can result in the
submodules being created using absolute paths internally, which will prevent the Vagrant
environment from provisioning correctly. To avoid this, clone the repository and the submodules
separately as two steps:
```
(git clone https://github.com/cartodb/cartodb ../cartodb && cd ../cartodb && git submodule update --init --recursive)
```

#### Copy configuration

Copy the template group vars file:
```
cp ansible/group_vars/all.example ansible/group_vars/all
```

#### Update hosts file

Add the following entries in your hosts file to point to the VM:
```
192.168.20.100 development.localhost.lan
192.168.20.100 commondata.localhost.lan
192.168.20.100 localhost.lan
```

If planning on using the organization account, you'll also need to add the following to your
hosts file:
```
192.168.20.100 test.localhost.lan
192.168.20.100 test-admin.localhost.lan
192.168.20.100 test-1.localhost.lan
192.168.20.100 test-2.localhost.lan
192.168.20.100 test-3.localhost.lan
192.168.20.100 test-4.localhost.lan
192.168.20.100 test-5.localhost.lan
```

## Using the VM

Run `vagrant up`, wait for the machine to provision, then navigate to http://development.localhost.lan/login
and login with:
 - Username: development
 - Password: cartodb

 Alternatively, if `cartodb_add_org_account: true` in ansible settings, you can navigate to http://test.localhost.lan/login
 and login with usernames `test-admin, test-1, test-2, test-3, test-4, test-5` where the password for each account
 is the same as the username.

#### Services in the VM

The cartodb services (unicorn, resque, sql api, windshaft) are controlled via upstart. The service
names are:
- `cartodb_server` - Unicorn web worker processes
- `cartodb_resque_worker` - A single resque worker process (the VM runs one but it can be configured via `cartodb_resque_workers` ansible setting)
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


## Troubleshooting

#### Data Library doesn't load on the dashboard

If for some reason after provision, the data library in the development account does not load
when you first navigate to your dashboard, try executing:
```
cd /opt/cartodb && bundle exec rake cartodb:remotes:reload['development']
```
inside the VM. Then navigate back to your dashboard once the rake task completes.
