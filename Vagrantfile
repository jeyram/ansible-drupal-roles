# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

# Use config.yml for basic VM configuration.
require 'yaml'
dir = File.dirname(File.expand_path(__FILE__))
if !File.exist?("#{dir}/site.yml")
  raise 'Configuration file not found! Please copy sample_yaml/example.site.yml to site.yml and try again.'
end
vconfig = YAML::load_file("#{dir}/group_vars/all")
# site.yml")[0]['vars']

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "ubuntu/bionic64"

  # Networking configuration.
  config.vm.network "forwarded_port", guest: 80, host: 8585,
  nic_type: "virtio"

  config.vm.hostname = vconfig['vagrant_hostname']
  if vconfig['vagrant_ip'] == "0.0.0.0" && Vagrant.has_plugin?("vagrant-auto_network")
    config.vm.network :private_network, :ip => vconfig['vagrant_ip'], :auto_network => true
  else
    config.vm.network :private_network, ip: vconfig['vagrant_ip']
  end

  config.ssh.forward_agent = true

  config.vm.synced_folder "www/", "/var/www", type: "nfs"

  config.vm.provider "virtualbox" do |vb|
    vb.memory = vconfig['vagrant_memory']
  end

  config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt update
  sudo apt upgrade -y
  which python || sudo apt -y install python
SHELL

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"
   # ansible.verbose = 'vvvv'
  end
end
