# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :private_network, ip: "192.168.33.16"

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--name", "supervisor", "--memory", "512"]
  end

  # Ansible provisioner.
  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "supervisor-demo.yml"
    ansible.host_key_checking = false
    ansible.verbose = "v"
  end
end