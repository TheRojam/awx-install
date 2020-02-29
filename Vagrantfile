ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.ssh.forward_agent = true
  config.vm.box = "bento/ubuntu-18.04"
  config.ssh.insert_key = false

  config.vm.provider :virtualbox do |v|
    v.memory = 4096
    v.cpus = 2
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
  end

  # AWX VM.
  config.vm.define "awx" do |awx|
    awx.vm.hostname = "awx.local"
    awx.vm.network :private_network, ip: "192.168.6.65"
    # Disable vagrant ssh and log into machine by ssh
    awx.vm.provision "file", source: "~/.ssh/id_testserver.pub", destination: "~/.ssh/authorized_keys"

    # Install Python to be able to provision machine using Ansible
    awx.vm.provision "shell", inline: "which python || sudo apt -y install python"
    
    awx.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provisioning/docker.yml"
      ansible.inventory_path = "provisioning/inventory"
      ansible.become = true
    end
   # Second playbook
    awx.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = "2.0"
      ansible.playbook = "provisioning/awx.yml"
      ansible.inventory_path = "provisioning/inventory"
      ansible.become = true
    end
    awx.vm.provision "shell", inline: "docker restart $(docker ps -qa)"
  end

end
