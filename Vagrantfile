# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # define provider preference
  config.vm.provider "virtualbox"
  config.vm.provider "vmware_fusion"

  # the source of this secure VM on https://github.com/dockpack/dockpack.git
  config.vm.box = "dockpack/centos6"
  config.vm.box_url = "https://atlas.hashicorp.com/dockpack/boxes/centos6"
  config.vm.box_check_update = true
  
  # we are using the standard insecure vagrant key (see ssh role)
  config.ssh.forward_agent = false
  config.ssh.insert_key = false
  config.vm.boot_timeout = 900
  config.vm.graceful_halt_timeout=100

## We don't use ansible as a vagrant provisioner, but it works like:
#
#  config.vm.provision "ansible" do |ansible|
#    ansible.inventory_path = "inventory/hosts"
#    ansible.playbook = "site.yml"
#    ansible.raw_arguments = "--ask-vault-pass"
#    ansible.verbose = "vv"
#    ansible.host_key_checking = "false"
#  end
###

  # this is the Ansible control host
  config.vm.define "control", primary: true do |control|
    control.vm.hostname = "control"
    control.vm.network "private_network", ip: "192.168.20.30"
    control.vm.synced_folder ".", "/home/vagrant/lunchbox", id: "vagrant-root", disabled: false
    #control.vm.synced_folder "./", "/home/vagrant/lunchbox", :nfs => true, :mount_options => ['vers=3','noatime','actimeo=2', 'tcp', 'fsc']
    control.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "512", "--natnet1", "172.16.1/24"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      vb.name = "control"
      vb.gui = false
    end   
  end
  
  # this is the frontend webserver host
  config.vm.define :web,  autostart: true do |web_config|
    web_config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
    web_config.vm.hostname = "web"
    web_config.vm.network "private_network", ip: "192.168.20.20", :netmask => "255.255.255.0",  auto_config: true
    web_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2020, auto_correct: true
    web_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024", "--natnet1", "172.16.1/24"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      vb.customize ["modifyvm", :id, "--ioapic", "on"  ]
      vb.name = "web"
      vb.gui = false
    end
  end

  # this is the backend postgresql database host
  config.vm.define :sql, autostart: true do |sql_config|
    sql_config.vm.synced_folder ".", "/vagrant", id: "vagrant-root", disabled: true
    sql_config.vm.hostname = "sql"
    sql_config.vm.network "private_network", ip: "192.168.20.22", :netmask => "255.255.255.0",  auto_config: true
    sql_config.vm.network "forwarded_port", id: 'ssh', guest: 22, host: 2022, auto_correct: true
    sql_config.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024", "--natnet1", "172.16.1/24"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      vb.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
      vb.gui = false
      vb.name = "sql"
    end
  end

end
