# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 1.6"

if ["up", "provision", "status"].include?(ARGV.first)
  require_relative "vagrant/ansible_galaxy_helper"

  AnsibleGalaxyHelper.install_dependent_roles("ansible")
end

ANSIBLE_ENV_GROUPS = {
  "development:children" => [
    "cartodb-servers"
  ]
}
VAGRANT_NETWORK_OPTIONS = { auto_correct: false }

ANSIBLE_GROUPS = {
  "cartodb-servers" => [ "cartodb" ]
}

Vagrant.configure("2") do |config|

  config.vm.define "cartodb" do |cartodb|
    cartodb.vm.box = "ubuntu/precise64"
    cartodb.vm.hostname = "cartodb"
    cartodb.vm.network "private_network", ip: "192.168.20.100"

    cartodb.vm.synced_folder ".", "/vagrant", disabled: true
    cartodb.vm.synced_folder "../cartodb", "/opt/cartodb", type: "nfs"

    cartodb.vm.provision "ansible" do |ansible|
      ansible.playbook = "ansible/cartodb.yml"
      ansible.groups = ANSIBLE_GROUPS.merge(ANSIBLE_ENV_GROUPS)
      ansible.raw_arguments = ["--timeout=60"]
    end

    cartodb.ssh.forward_x11 = true

    cartodb.vm.provider :virtualbox do |v|
      v.memory = ENV.fetch("CARTODB_MEM", 4096)
      v.cpus = ENV.fetch("CARTODB_DATABASE_CPU", 3)
    end
  end
end
