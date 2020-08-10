# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "hashicorp/bionic64"
    ubuntu.vm.hostname = 'ubuntu'

    ubuntu.vm.network :private_network, ip: "192.168.56.100"

    ubuntu.vm.provider :virtualbox do |v|
      v.natdnshostresolver1 = "on"
      v.memory = 1024
      v.name = "ubuntu"
    end
  end