# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  config.vm.box = "debian/jessie64" 
    config.vm.define "Zabbix" do |mon|
      mon.vm.provider "virtualbox" do |v|
        v.memory        = 1024
        v.cpus          = 1
      end
      mon.vm.network "public_network", bridge: "en1: eth1"
  end

end
