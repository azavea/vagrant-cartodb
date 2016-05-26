## 0.2.0
- Upgrades to postgresql 9.5
- Default group_vars now track CartoDB master branch rather than a specific version. The primary
  goal of this project is to provide a easy to spin up dev environment for CartoDB. This is
  better served by staying up to date with master rather than a specific old version.
- Installs cartodb-postgresql dependency from cartodb submodule rather than a separate git clone
- Templates app_config.yml and database.yml so that cartodb configuration can be set via
  ansible variables
- Can modify some more settings via ENV
- Change env CARTODB_DATABASE_CPU to CARTODB_CPUS

If upgrading from a previous version, it is best to update your group vars and recreate the vm:
```
vagrant destroy
cp ansible/group_vars/all.example ansible/group_vars/all
vagrant up
```
If you'd rather attempt an in place upgrade, further tips are here:
https://github.com/azavea/vagrant-cartodb/commit/f73241d30dd1ac2abe54488bad5172de71f2f451


## 0.1.0

- Initial release
- Supports CartoDB 3.12.2
