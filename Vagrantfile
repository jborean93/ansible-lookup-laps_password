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

    # This also provisions DC01
    child.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "windows"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end

  config.vm.define "centos" do |centos|
    centos.vm.box = "centos/7"
    centos.vm.hostname = "CENTOS"
    centos.vm.network :private_network, ip: "192.168.56.52", :name => 'vboxnet0'
    centos.vm.provider :virtualbox do |v|
      v.gui = false
      v.cpus = 2
    end

    centos.vm.provision "shell", inline: "yum install -y epel-release", privileged: true

    centos.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "centos"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end

  config.vm.define "fedora" do |fedora|
    fedora.vm.box = "generic/fedora29"
    fedora.vm.hostname = "FEDORA"
    fedora.vm.network :private_network, ip: "192.168.56.53", :name => 'vboxnet0'
    fedora.vm.provider :virtualbox do |v|
      v.gui = false
      v.cpus = 2
    end

    fedora.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "fedora"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end

  config.vm.define "ubuntu" do |ubuntu|
    ubuntu.vm.box = "ubuntu/xenial64"
    ubuntu.vm.hostname = "UBUNTU"
    ubuntu.vm.network :private_network, ip: "192.168.56.54", :name => 'vboxnet0'
    ubuntu.vm.provider :virtualbox do |v|
      v.gui = false
      v.cpus = 2
    end

    ubuntu.vm.provision "shell", inline: "apt-get update -y", privileged: true

    ubuntu.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "ubuntu"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end

  config.vm.define "arch" do |arch|
    arch.vm.box = "archlinux/archlinux"
    arch.vm.hostname = "ARCH"
    arch.vm.network :private_network, ip: "192.168.56.55", :name => 'vboxnet0'
    arch.vm.provider :virtualbox do |v|
      v.gui = false
      v.cpus = 2
    end

    arch.vm.provision "shell", inline: "pacman -Sy --needed --noconfirm python", privileged: true

    arch.vm.provision "ansible" do |ansible|
      ansible.playbook = "main.yml"
      ansible.limit = "arch"
      ansible.inventory_path = "inventory.ini"
      ansible.verbose = "-vv"
    end
  end
end
