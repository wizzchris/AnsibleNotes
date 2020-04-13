# Ansible Notes
## **Introduction**
In this repository I go over the configuration tool Ansible and the different commands available to it.
I also go over examples with Ansible provisioning an ubuntu 18 environment.


 ## **Installation**
 ### Shell
 To install Ansible in shell first run these commands:
 ````
 sudo apt-get update
 sudo apt-get upgrade -y
 ````  
 Ensure python is installed with this command:
````
sudo apt-get install python -y
````
 Then run this command to install Ansible:
 ````
 sudo apt-get install ansible -y
 ````
### Via Vagranthost
This method allows you to install Ansible upon vagrant up. This allows you to also run the playbook to provision the environment.
to do this add this to the Vagrantfile:
````
config.vm.provision "ansible" do |ansible|
  ansible.playbook = "playbook.yml"
````
This also runs the playbook called playbook.yml.
There are additional configurations you can add upon installing Ansible. Use this website to find them:
https://www.vagrantup.com/docs/provisioning/ansible.html
## **Configuration**
### Ansible Files
##### *Playbook*
A playbook is the list of commands run on the control node as well as managed nodes. You can specify what nodes to run on. In the Playbook section we see a list of commands you can use
##### *Inventory*
This lists the managed nodes. This is also called the hostfile. This lists the ip address of each node. You can nest each node into groups to better provision many nodes. In the Inventory section we see how this is done
##### *Ansible Configuration*
This is the file ansible.cfg, this is automatically created and should be appropriate.
Please follow this link for more information on how to change the config file if needed:
https://docs.ansible.com/ansible/latest/reference_appendices/config.html#ansible-configuration-settings
### Playbook
The playbook is the list of commands to run. It is written in YAML. Each playbook is comprised of one pr more plays. The goal of a play is to run a series of tasks. You can specify the hosts to which the playbook to run. if the host is
````
hosts: all
````
then the playbook is run on all the hosts.
You can specify the host or ip by changing the all. To add multiple hosts use a colon :,
````
hosts: host1:host2:host3
````
 to exclude a host use a colon than explanation mark :!,
````
hosts: host1:host2:!host3
````
  finally use an ampersand to only include the interseciton :&.
````
hosts: group1:&group2
````

In the playbook each task is played in order, from top to bottom. The goal of each task is to execute a task.

We can use variables in the playbook as well.

To run a playbook do the command:
````
ansible-playbook playbook.yml
````

However it is better for a node to pull the configuration, to do this run the command:
````
ansible-pull
````
This scales infinitely so is good when scaling.
This list of nodes you can use are in this link:
https://docs.ansible.com/ansible/latest/modules/list_of_all_modules.html#all-modules

You can split playbooks up then use the
````
include
import
````
commands to include multiple playbooks.

You can use become to change the user.

The playbook for the ansible host node in this repository is:
````
---
- hosts: node1
  become: yes
  tasks:

  - name: Update Apt and Upgrade Apt
    apt:
      update_cache: yes
      upgrade: dist

  - name: Install Java
    apt:
      name: default-jre

  - name: Copy private key to keys
    copy:
      src: /home/vagrant/key/id_rsa
      dest: /home/vagrant/.ssh/id_rsa
      mode: 0777

  - name: Change inventory file
    shell: 'cat /home/vagrant/ansibles/inventory.yml > /etc/ansible/hosts'

````
To see the apps, database and loadbalacners playbook please click on the playbooks in the ansibles file.

### Inventory
To make an inventory we can either make a new one in /etc/ansible/ as a hosts file. Then add the ip addresses. This can be done either via YMAL or INI.

Ansible communicates over SSH so ensure that the public key is added to the authorized_keys on those systems.

You can add group names by using square brackets or by specifying new group in YAML. You can add the same host in multiple groups and even add a group to a new group.
The inventory file in this vagrant file is this:
````
all:
  hosts:
    node1:
      ansible_connection: local
  children:
    Apps:
      hosts:
        node2:
          ansible_host: 10.0.10.12
        node3:
          ansible_host: 10.0.10.13
    Data_Bases:
      hosts:
        node4:
          ansible_host: 10.0.10.14
        node5:
          ansible_host: 10.0.10.15
        node6:
          ansible_host: 10.0.10.16
    Load_Balancer:
      hosts:
        node7:
          ansible_host: 10.0.10.17
  vars:
    ansible_connection: ssh
    ansible_host: vagrant
    ansible_user: vagrant
    ansible_ssh_private_key_file: /home/vagrant/.ssh/id_rsa
    host_key_checking: False
    ansible_ssh_common_args: '-o StrictHostKeyChecking=no'
    ansible_python_interpreter: /usr/bin/python3

````

You can also add variables to each host by adding a vars section to the hosts file. This allows multiple variables added to the connection line to multiple hosts.

The vars file can also be separate to better manage the variables of the nodes. for variable commands please follow the below link:
https://docs.ansible.com/ansible/latest/user_guide/intro_inventory.html#connecting-to-hosts-behavioral-inventory-parameters

Multiple inventory files can also be used, you will just need to specify them in the command line.

## **Ansible Vault**
Ansible vault is a feature of ansible that allows you to keep sensitive data in encrypted files rather than plaintext. These vault files can then be distributed or placed in source control.
To access the vault you'll need a password and will have to add the variable:
````
--valut-password-file or --vault-id
````
You can also specify where the password is a file.
To create a new encrypted file you run the command:
````
ansible-vault create name.YAMl
````
This will prompt you for a password. Use the flag
````
--vault-id password
````
to decide what password you want to use.
You can encrypt a file by running the command:
````
ansible-vault encrypt files
````
and decrypt with
````
ansible-vault decrypt files
````
To provide a password use these flags
````
--ask-vault-pass
--vault-password-file
````
ask vault pass will prompt for a password whilst vault password file will need a location for the password.

For more information use this link:
https://docs.ansible.com/ansible/latest/user_guide/vault.html
