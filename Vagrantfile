ENV['VAGRANT_DEFAULT_PROVIDER'] = 'virtualbox'
Vagrant.configure("2") do |config|

    config.vm.define "awx" do |web|

        # Resources for this machine
        web.vm.provider "virtualbox" do |vb|
           vb.memory = "2048"
           vb.cpus = "1"
        end

        web.vm.box = "bento/ubuntu-18.04"

        web.vm.hostname = "awx"

        # Define public network. If not present, Vagrant will ask.
        web.vm.network "public_network", bridge: "Intel(R) Dual Band Wireless-AC 7260"

        # Disable vagrant ssh and log into machine by ssh
        web.vm.provision "file", source: "~/.ssh/id_rsa.pub", destination: "~/.ssh/authorized_keys"

        # Install Python to be able to provision machine using Ansible
        web.vm.provision "shell", inline: "which python || sudo apt -y install python"

        # Add artifactory address as not using dns
        web.vm.provision "shell", inline: "sudo echo 192.168.1.6 artifactory docker.artifactory docker-remote.artifactory docker-local.artifactory | sudo tee -a /etc/hosts"
        
        # copy key for artifactory
        config.vm.provision "file", source: "./awx/nginx-config/artifactory.key", destination: "~/artifactory.key"

        # copy certs for artifactory
        config.vm.provision "file", source: "./awx/nginx-config/artifactory.crt", destination: "~/artifactory.crt"

        # Add artifactory certs for docker
        web.vm.provision "shell", inline: "sudo mv /home/vagrant/artifactory.key /usr/local/share/ca-certificates/artifactory.key"

        # Add artifactory certs for docker
        web.vm.provision "shell", inline: "sudo mv /home/vagrant/artifactory.crt /usr/local/share/ca-certificates/artifactory.crt"

        # Add artifactory certificates
        web.vm.provision "shell", inline: "sudo update-ca-certificates"

    end

end
