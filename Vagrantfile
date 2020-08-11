# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  config.vm.define "ubuntu01" do |ubuntu01|
    ubuntu01.vm.box = "hashicorp/bionic64"
    ubuntu01.vm.hostname = 'ubuntu01'

    ubuntu01.vm.define "centos01"
    ubuntu01.vm.define "centos02"

    ubuntu01.vm.network :private_network, ip: "192.168.77.10"

    ubuntu01.vm.provider :virtualbox do |v|
      #v.natdnshostresolver1 = "on"
      v.memory = 2024
      v.name = "ubuntu01"
    end

    ubuntu01.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update --yes
      sudo apt-get install --yes software-properties-common
      sudo apt-add-repository --yes --update ppa:ansible/ansible
      sudo apt-get install --yes ansible
      sudo apt-get install --yes python3-pip libssl-dev

      sudo ansible-galaxy install haxorof.docker_ce

      sudo ssh-keygen -N "" -t rsa -b 4096 -f /home/vagrant/.ssh/id_rsa
      sudo sshpass -p vagrant ssh-copy-id vagrant@192.168.77.11 
      sudo sshpass -p vagrant ssh-copy-id vagrant@192.168.77.12 

      printf "\n[kiratech]\n192.168.77.11\n192.168.77.12\n" | sudo tee -a /etc/ansible/hosts

    SHELL

    #ubuntu01.vm.provision "ansible_local" do |ansible|
    #  ansible.become = true
    #  ansible.install = true
    #  ansible.playbook = "playbook.yml"
    #  ansible.groups = {
    #    "kiratech" => [ "centos01","centos02"
    #  ]}
    #  ansible.galaxy_role_file = "requirements.yml"
    #  ansible.galaxy_roles_path = "/etc/ansible/roles"
    #  ansible.galaxy_command = "sudo ansible-galaxy install haxorof.docker_ce"
    #end
  end

  N = 1
  (1..N).each do |machine_id|

    config.vm.define "centos0#{machine_id}" do |machine|
      machine.vm.box = "centos/8"
      machine.vm.hostname = "centos0#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.77.#{10+machine_id}"

      machine.vm.provider :virtualbox do |v|
        #v.natdnshostresolver1 = "on"
        v.memory = 1024
        v.name = "centos0#{machine_id}"
      end

      config.vm.provision "shell", inline: <<-SHELL
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
        systemctl restart sshd.service
      SHELL

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      #if machine_id == N
      #  machine.vm.provision :ansible_local do |ansible|
      #    # Disable default limit to connect to all the machines
      #    ansible.install = true
      #    ansible.limit = "all"
      #    ansible.galaxy_command = "sudo ansible-galaxy install haxorof.docker_ce"
      #    ansible.playbook = "play-role-docker.yml"
      #  end
      #end
    end
  end
end