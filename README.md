# README
This repository is my notes as well as work for ansible.
To see the work, download virtualbox as well as varant on your host computer. Then, in this repository run the command:
````
vagrant up
````
After which go into node1 with the command:
````
vagrant ssh node1
````
This will log you into node1 which is the host node.
Run these comands to provision the databases and app
````
cd ansibles
ansible-playbook dbplaybook.yml
ansible-playbook appplaybook.yml
````

The database will create a database replication server.
## Files
The files are:
- ansibles = file used for synching

- key_pub = is the public key added to each of the nodes
- AnsiblesNotes = are my notes on ansible
- vagrantfile = the vagrant file for vagrant up

### Ansibles
