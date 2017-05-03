# -*- mode: ruby -*-
# vi: set ft=ruby :

NODE_COUNT = 1

Vagrant.configure("2") do |config|
  config.vm.box = "bento/centos-7.2"
  config.ssh.forward_agent = true
  config.vm.provision "shell", path: "install-common-package.sh"
  config.vm.provision "shell", path: "install-docker.sh"
  config.vm.provision "shell", path: "install-kubeadm.sh"

  MASTER_ADDRESS = "192.168.33.10"

  config.vm.provider :virtualbox do |vb|
    vb.customize ["modifyvm", :id, "--memory", "2048"]
  end

  config.vm.define :master do |host|
    _HOSTNAME = "master"
    _PRIVATE_IP_ADDRESS = MASTER_ADDRESS

    host.vm.hostname = _HOSTNAME
    host.vm.network "private_network", ip: _PRIVATE_IP_ADDRESS
    host.vm.provision "shell", path: "init-kubeadm.sh"
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network "private_network", ip: "192.168.33.1#{i}"
      subconfig.vm.provision "shell" do |s|
        s.path = "join-kubeadm.sh"
        s.args = MASTER_ADDRESS
      end
    end
  end
end
