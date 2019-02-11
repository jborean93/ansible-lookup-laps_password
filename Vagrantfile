# _*_ mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.define "dc" do |dc|
    dc.vm.box = "jborean93/WindowsServer2019"
    dc.vm.hostname = "DC01"
    dc.vm.network :private_network, ip: "192.168.56.50", :name => 'vboxnet0'
    dc.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 2048
      v.cpus = 2
    end
  end

  config.vm.define "child" do |child|
    child.vm.box = "jborean93/WindowsServer2019"
    child.vm.hostname = "CHILD01"
    child.vm.network :private_network, ip: "192.168.56.51", :name => 'vboxnet0'
    child.vm.provider :virtualbox do |v|
      v.gui = false
      v.memory = 2048
      v.cpus = 2
    end

    child.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "all"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end
end
