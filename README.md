# ansible-cartodb
Ansible role to build a multi-machine vagrant setup for CartoDB

Currently targets tag 3.12.2 on January 29, 2016

## Requirements

Before running `vagrant up`, do the following:


#### Install dependencies

On your host machine, install:
  - vagrant 1.6+
  - ansible 1.8.4+


#### Clone CartoDB

Clone the CartoDB repo to `../cartodb`:
```
git clone --branch 3.12.2 --recursive https://github.com/cartodb/cartodb ../cartodb
```
If this path is already in use for some reason, you can clone the repo elsewhere,
and update the path in the Vagrantfile.

#### Copy configuration

Copy the template group vars file:
```
cp ansible/group_vars/all.example ansible/group_vars/all
```

#### Copy CartoDB app configuration (optional)

This step is only necessary if you will be developing on the front-end assets.
```
cp ../cartodb/config/app_config.yml.sample ../cartodb/config/app_config.yml
```
Open the copied app_config.yml file and comment out the lines that look like this:
```

app_assets:
  asset_host: "//cartodb-libs.global.ssl.fastly.net/cartodbui"
# should become...

# app_assets:
# asset_host: "//cartodb-libs.global.ssl.fastly.net/cartodbui"

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
versions of the sql api, windshaft, and the postgresql extension in `ansible/group_vars/all`

For any recent version of cartodb, setting:
```
cartodb_sqlapi_version: master
cartodb_windshaft_version: master
```
is likely a safe bet.
