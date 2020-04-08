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
Run these commands to provision the databases and app
````
cd ansibles
ansible-playbook dbplaybook.yml
ansible-playbook appplaybook.yml
ansible-playbook lbplaybook.yml
````
You can also run these commands to give the same result
````
cd ansibles
ansible-playbook masterplaybook.yml
````
This will provision a database replication set and app instances with a load balancer.
To see the app you will need to log into one of the apps and seed the database. To do this run these commands
````
cd App/app
node seeds/seed.js
````
You will then need to log into both apps and run these commands
````
cd App/app
npm start &
````
You can then log into the ip of the load balancer and see the app with the posts page
## Files
The files are:
- ansibles = file used for synching

- key_pub = is the public key added to each of the nodes
- AnsiblesNotes = are my notes on ansible
- vagrantfile = the vagrant file for vagrant up

### Ansibles
This section will go through the files in the ansibles file.
- appplaybook.yml = the playbook for the app nodes
- dbplaybook.yml = the playbook for the database nodes
- inventory.yml = the hosts file for ansible. It uses this file to find locations on the nodes
- mongod.conf = the configuration file for mongo db
- playbook.yml = the playbook for the host node
