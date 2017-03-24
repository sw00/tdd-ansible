# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box_check_update = false

  config.vm.define "control", primary: true do |control|
    control.vm.box = "ubuntu/xenial32"
    control.vm.hostname = "control"

    control.vm.network "private_network", ip: "192.168.33.10"

    control.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.memory = "1024"
    end

    control.vm.provision "file", source: "control_ssh_key", destination: ".ssh/id_rsa"

    control.vm.provision "shell", inline: <<-SHELL
       sudo apt-get install -y software-properties-common
       sudo apt-add-repository ppa:ansible/ansible
       sudo apt-get update
       sudo apt-get install -y ansible
     SHELL
  end

  config.vm.define "webserver" do |webserver|
    webserver.vm.box = "ubuntu/xenial32"
    webserver.vm.hostname = "webserver"

    webserver.vm.network "private_network", ip: "192.168.33.20"

    webserver.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.memory = "1024"
    end

    webserver.vm.provision "shell", inline: <<-SHELL
      cat /vagrant/control_ssh_key.pub >> /home/ubuntu/.ssh/authorized_keys
    SHELL
  end

end
