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

BOX_IMAGE = "bento/centos-7.3"
NODE_COUNT = 3
ANSIBLE_VERSION = "2.0"

VAGRANT_CONFIG = File.join(Dir.pwd, "vagrant.cfg")

Vagrant.configure("2") do |config|
  config.hostmanager.enabled = true
  config.hostmanager.manage_host = true
  config.hostmanager.manage_guest = true
  config.hostmanager.ignore_private_ip = false
  config.hostmanager.include_offline = true

  if File.file?(VAGRANT_CONFIG) == false
    puts "======================================================================"\
         "========="
    puts "Setting up Default Environment Variables..."
    puts "======================================================================"\
         "========="

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
      "Ansible_Playbook_Path": ANSIBLE_PLAYBOOK_PATH,
      "Ansible_Inventory": ANSIBLE_INVENTORY
    }
    File.open(VAGRANT_CONFIG, 'w') {|f| f.write vagrantcfg.to_yaml }
    puts ""
  else
    settings = YAML::load_file(VAGRANT_CONFIG)
    ANSIBLE_CONFIG = settings[:Ansible_Config]
    ANSIBLE_PLAYBOOK_PATH = settings[:Ansible_Playbook_Path]
    ANSIBLE_INVENTORY = settings[:Ansible_Inventory]
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "node#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "node#{i}"
      subconfig.vm.network :private_network, ip: "172.128.1.#{i + 29}"
      subconfig.vm.provider :virtualbox do |vb|
        vb.name = "node#{i}"
        vb.customize ["modifyvm", :id, "--memory", "2048"]
        vb.customize ["modifyvm", :id, "--cpus", "2"]
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      subconfig.vm.provision :ansible do |ansible|
        ansible.compatibility_mode = ANSIBLE_VERSION
        ansible.config_file = ANSIBLE_CONFIG
        ansible.playbook = File.join(ANSIBLE_PLAYBOOK_PATH, "nodes.yml")
        ansible.inventory_path = ANSIBLE_INVENTORY
        #ansible.ask_vault_pass = TRUE
        ansible.vault_password_file = "/Users/ccarlet1/neolith/.edmspass"
        #ansible.verbose = '-vvv'
      end
      subconfig.ssh.username = 'root'
      subconfig.ssh.password = 'vagrant'
      subconfig.ssh.insert_key = 'true'
    end
  end

  (1..NODE_COUNT).each do |i|
    config.vm.define "lnode#{i}" do |subconfig|
      subconfig.vm.box = BOX_IMAGE
      subconfig.vm.hostname = "lnode#{i}"
      subconfig.vm.network :private_network, ip: "172.128.1.#{i + 39}"
      subconfig.vm.provider :virtualbox do |vb|
        vb.name = "lnode#{i}"
        vb.customize ["modifyvm", :id, "--memory", "10240"]
        vb.customize ["modifyvm", :id, "--cpus", "4"]
        vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      end
      subconfig.vm.provision :ansible do |ansible|
        ansible.compatibility_mode = ANSIBLE_VERSION
        ansible.config_file = ANSIBLE_CONFIG
        ansible.playbook = File.join(ANSIBLE_PLAYBOOK_PATH, "lnodes.yml")
        ansible.inventory_path = ANSIBLE_INVENTORY
        #ansible.verbose = '-vvv'
      end
      subconfig.vm.synced_folder "/Users/ccarlet1/vagrant/sync", "/sync"
      subconfig.ssh.username = 'root'
      subconfig.ssh.password = 'vagrant'
      subconfig.ssh.insert_key = 'true'
    end
  end
end

################################################################################
############# Auto adjust playbook hosts for vagarnt, to add later #############
################################################################################
# node_playbook = File.join(ANSIBLE_PLAYBOOK_PATH, "nodes.yml")
# contents = File.read(node_playbook)
# playbookcfg = contents.gsub(/\${NODE}/, "node#{i}"
# File.open(node_playbook, "w") {|file| file.write(playbookcfg)}
#
# contents = File.read(node_playbook)
# playbookcfg = contents.gsub(/node#{i}$/, "${NODE}")
# File.open(node_playbook, "w") {|file| file.write(playbookcfg)}
