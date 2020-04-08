required_plugins = ["vagrant-hostsupdater"] #This ensurs we can change the name of the ip name
required_plugins.each do |plugin|
    exec "vagrant plugin install #{plugin}" unless Vagrant.has_plugin? plugin
end
nodes = ["2","3","4", "5","6","7","8","9","10"] #This is the names of the nodes you want
Vagrant.configure("2") do |config|

  config.vm.define "node1" do |node1|
    node1.vm.box = "ubuntu/bionic64"
    node1.vm.network "private_network", ip: "10.0.10.11"
    node1.hostsupdater.aliases = ["node1.local"]
    node1.vm.synced_folder "ansibles", "/home/vagrant/ansibles/"
    node1.vm.synced_folder "key", "/home/vagrant/key/"
    node1.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "/home/vagrant/ansibles/playbook.yml"
      ansible.inventory_path = "/home/vagrant/ansibles/inventory.yml"
      ansible.extra_vars = { ansible_python_interpreter:"/usr/bin/python3" }
    end
    node1.vm.provider "virtualbox" do |v|
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end
  end
  nodes.each do |node|
    config.vm.define "node#{+ node}" do |nod|
      nod.vm.box = "ubuntu/bionic64"
      nod.vm.network "private_network", ip: "10.0.10.1#{+node}"
      nod.hostsupdater.aliases = ["node#{+ node}.local"]
      nod.vm.synced_folder "key_pub", "/home/vagrant/key_pub/"
      nod.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
        v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      end
      nod.vm.provision "shell" do |s|
        s.inline = "cat /home/vagrant/key_pub/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys"
      end
    end
  end
end
