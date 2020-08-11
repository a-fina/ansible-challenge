# Provisioning 2 CentOS and 1 Ubuntu VM install Docker with Ansible
vagrant plugin install vagrant-disksize

vagrant up

## Login into Control Node
vagrant ssh ubuntu01

## Login into Controlled Node
vagrant ssh centos01 

vagrant ssh centos02 

## Molecule test
molecule init role my-new-role

cd my-new-role
 
molecule test

## Link
    https://www.linode.com/docs/applications/configuration-management/getting-started-with-ansible/#set-up-the-control-node
