# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
# -*- mode: ruby -*-
# vi: set ft=ruby :

# Every Vagrant development environment requires a box. You can search for
# boxes at https://atlas.hashicorp.com/search.
require 'yaml'

BOX_IMAGE = "bento/centos-7.4"
NODE_COUNT = 3
ANSIBLE_VERSION = "2.0"

VAGRANT_CONFIG = File.join(Dir.pwd, "vagrant.cfg")

if File.file?(VAGRANT_CONFIG) == false
  puts "========================================================================"\
       "======="
  puts "Setting up Default Environment Variables..."
  puts "========================================================================"\
       "======="

  ANSIBLE_DIRECTORY = File.join(Dir.pwd, "ansible")
  ANSIBLE_INVENTORY = File.join(ANSIBLE_DIRECTORY, "inventory")
  ANSIBLE_ROLES = File.join(ANSIBLE_DIRECTORY, "roles")
  ANSIBLE_LOG_PATH = File.join(ANSIBLE_DIRECTORY, "logs")
  ANSIBLE_CONFIG = File.join(ANSIBLE_DIRECTORY, "ansible.cfg")
  ANSIBLE_PLAYBOOK_PATH = File.join(ANSIBLE_DIRECTORY, "playbooks")

  ansiblecfg = [
    [/\${ANSIBLE_INVENTORY}/, "Inventory", ANSIBLE_INVENTORY],
    [/\${ANSIBLE_ROLES}/, "Roles Path", ANSIBLE_ROLES],
    [/\${ANSIBLE_LOG_PATH}/, "Log Path", ANSIBLE_LOG_PATH]
  ]

  ansiblecfg.each do |update|
    contents = File.read(ANSIBLE_CONFIG)
    configure = contents.gsub(update[0], update[2])
    puts "==> " + update[1] + ": " + update[2]
    File.open(ANSIBLE_CONFIG, "w") {|file| file.write(configure)}
  end

  vagrantcfg = {
    "Ansible_Config": ANSIBLE_CONFIG,
    "Ansible_Playbook_Path": ANSIBLE_PLAYBOOK_PATH
  }
  File.open(VAGRANT_CONFIG, 'w') {|f| f.write vagrantcfg.to_yaml }

else
  puts "========================================================================"\
       "======="
  puts "Loading Settings for vagrant.cfg..."
  puts "========================================================================"\
       "======="
  settings = YAML::load_file(VAGRANT_CONFIG)
  ANSIBLE_CONFIG = settings[:Ansible_Config]
  ANSIBLE_PLAYBOOK_PATH = settings[:Ansible_Playbook_Path]
end

puts ""

Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = false
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  config.vm.define "puppet" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "puppet-test"
    subconfig.vm.network :private_network, ip: "172.128.1.10"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "puppet"
      vb.customize ["modifyvm", :id, "--memory", "2048"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "puppet-server.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "elasticdeb" do |subconfig|
    subconfig.vm.box = "bento/ubuntu-17.10"
    subconfig.vm.hostname = "elasticdeb"
    subconfig.vm.network :private_network, ip: "172.128.1.5"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "pi"
      vb.customize ["modifyvm", :id, "--memory", "2048"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "elasticdeb.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "jenkins" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "jenkins"
    subconfig.vm.network :private_network, ip: "172.128.1.11"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "jenkins"
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--cpus", "1"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "jenkins.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "elk" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "elk"
    subconfig.vm.network :private_network, ip: "172.128.1.12"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "elk"
      vb.customize ["modifyvm", :id, "--memory", "4096"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "elk.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "elastic" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "elastic"
    subconfig.vm.network :private_network, ip: "172.128.1.13"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "elastic"
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "elastic.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "prometheus" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "prometheus"
    subconfig.vm.network :private_network, ip: "172.128.1.14"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "prometheus"
      vb.customize ["modifyvm", :id, "--memory", "2048"]
      vb.customize ["modifyvm", :id, "--cpus", "2"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "prometheus.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  config.vm.define "grafana" do |subconfig|
    subconfig.vm.box = BOX_IMAGE
    subconfig.vm.hostname = "grafana"
    subconfig.vm.network :private_network, ip: "172.128.1.15"
    subconfig.vm.provider :virtualbox do |vb|
      vb.name = "grafana"
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--cpus", "1"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end
    subconfig.vm.provision :ansible do |ansible|
      ansible.compatibility_mode = ANSIBLE_VERSION
      ansible.config_file = ANSIBLE_CONFIG
      ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "grafana.yml"
    end
    subconfig.ssh.username = 'root'
    subconfig.ssh.password = 'vagrant'
    subconfig.ssh.insert_key = 'true'
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network :private_network, ip: "172.128.1.#{i + 19}"
      subconfig.vm.provider :virtualbox do |vb|
        vb.name = "node#{i}"
        vb.customize ["modifyvm", :id, "--memory", "4096"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      subconfig.vm.provision :ansible do |ansible|
        ansible.compatibility_mode = ANSIBLE_VERSION
        ansible.config_file = ANSIBLE_CONFIG
        ansible.playbook = ANSIBLE_PLAYBOOK_PATH + "nodes.yml"
      end
      subconfig.ssh.username = 'root'
      subconfig.ssh.password = 'vagrant'
      subconfig.ssh.insert_key = 'true'
    end
  end
end
