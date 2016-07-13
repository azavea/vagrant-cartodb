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

MOUNT_OPTIONS = if Vagrant::Util::Platform.linux? then
                  ['rw', 'vers=4', 'tcp', 'nolock']
                else
                  ['vers=3', 'udp']
                end

ROOT_VM_DIR = "/opt/cartodb"

Vagrant.configure("2") do |config|

  config.vm.define "cartodb" do |cartodb|
    cartodb.vm.box = "ubuntu/precise64"
    cartodb.vm.hostname = "cartodb"
    cartodb.vm.network "private_network", ip: ENV.fetch("CARTODB_PRIVATE_IP", "192.168.20.100")

    cartodb.vm.synced_folder ".", "/vagrant", disabled: true
    cartodb.vm.synced_folder ENV.fetch("CARTODB_SRC_DIR", "../cartodb"), ROOT_VM_DIR, type: "nfs", mount_options: MOUNT_OPTIONS

    cartodb.vm.provision "bootstrap", type: "ansible" do |ansible|
      ansible.playbook = "ansible/cartodb.yml"
      ansible.groups = ANSIBLE_GROUPS.merge(ANSIBLE_ENV_GROUPS)
      ansible.raw_arguments = ["--timeout=60"]
    end

    # Restart some cartodb services on each boot
    # Needs to be after ansible provision and also in a block definition so this runs after ansible
    cartodb.vm.provision "restart_services", type: "shell", run: "always" do |s|
      s.inline = "service cartodb_server restart && service cartodb_resque_worker restart WORKERNUM=1"
    end
    cartodb.vm.provision "restore_redis", type: "shell", run: "always" do |s|
      s.privileged = false
      s.inline = "cd #{ROOT_VM_DIR} && ./script/restore_redis"
    end

    cartodb.ssh.forward_x11 = true

    cartodb.vm.provider :virtualbox do |v|
      v.memory = ENV.fetch("CARTODB_MEM", 4096)
      v.cpus = ENV.fetch("CARTODB_CPUS", 3)
    end
  end
end
