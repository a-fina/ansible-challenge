# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # CentOS
  N = 1
  (1..N).each do |machine_id|

    config.vm.define "centos0#{machine_id}" do |machine|
      machine.vm.box = "centos/8"
      machine.vm.hostname = "centos0#{machine_id}"
      machine.vm.network "private_network", ip: "192.168.77.#{10+machine_id}"
      machine.disksize.size = '50GB'

      machine.vm.provider :virtualbox do |v|
        v.memory = 1024
        v.name = "centos0#{machine_id}"
      end

      config.vm.provision "shell", inline: <<-SHELL
        sed -i 's/PasswordAuthentication no/PasswordAuthentication yes/g' /etc/ssh/sshd_config    
        systemctl restart sshd.service
      SHELL

      # Only execute once the Ansible provisioner,
      # when all the machines are up and ready.
      #if machine_id == N_local
      #  machine.vm.provision :ansible do |ansible|
      #    # Disablinstall = trueit to connect to all the machines
      #    ansible.limit"all
      #    ansible.playbook = "play-role-docker.yml"
      #    ansible.galaxy_command = "sudo ansible-galaxy install haxorof.docker_ce"
      #  end
      #end
    end
  end

  # Ubuntu control Node
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
      apt-get update --yes
      apt-get install --yes software-properties-common
      apt-add-repository --yes --update ppa:ansible/ansible
      apt-get install --yes ansible

      apt-get install --yes build-essential libssl-dev libffi-dev python3-dev python3-pip
      python3 -m pip install --upgrade --user setuptools
      python3 -m pip install --user "molecule[lint]"
      python3 -m pip install -U git+https://github.com/ansible-community/molecule

      whoami

      ssh-keygen -N "" -t rsa -b 4096 -f /home/vagrant/.ssh/id_rsa <<< y
      sshpass -p vagrant ssh-copy-id -i /home/vagrant/.ssh/id_rsa root@192.168.77.11 
      ssh-keyscan -H 192.168.77.11 >> ~/.ssh/known_hosts

      printf "\n[kiratech]\n" | sudo tee -a /etc/ansible/hosts
      printf "\n192.168.77.11 ansible_ssh_pass=vagrant ansible_ssh_user=root\n" | sudo tee -a /etc/ansible/hosts

      ansible all -m ping
      ansible-galaxy install haxorof.docker_ce
      ansible-playbook /vagrant/ansible-playbook/playbook-role-docker.yml

      ansible-galaxy install git+https://github.com/ExperitestOfficial/ansible-role-disk-space-validator
      ansible-playbook /vagrant/ansible-playbook/playbook-disk-validator.yml

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

end