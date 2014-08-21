# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

BOX_MEM = "2048"
BOX_NAME =  "precise64"
BOX_URI = "http://files.vagrantup.com/precise64.box"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  # config.vm.box = "precise64"

  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  # config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.define :server1 do |server1_config|
    server1_config.vm.box = BOX_NAME
    server1_config.vm.box_url = BOX_URI
    server1_config.vm.network :private_network, ip: "10.1.42.30"
	server1_config.proxy.http  = "http://www-proxy.ericsson.se:8080"
	server1_config.proxy.https = "http://www-proxy.ericsson.se:8080"    
    server1_config.vm.network :forwarded_port, host: 45680, guest: 8000
    server1_config.vm.hostname = "shipyardserver.local"
    server1_config.ssh.forward_agent = true
    server1_config.vm.provider "virtualbox" do |v|
      v.name = "shipyardserver"
      v.customize ["modifyvm", :id, "--memory", 4096]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end  
  end
  
  config.vm.define :node1 do |node1_config|
    node1_config.vm.box = BOX_NAME
    node1_config.vm.box_url = BOX_URI
    node1_config.vm.network :private_network, ip: "10.1.42.10"
	node1_config.proxy.http  = "http://www-proxy.ericsson.se:8080"
	node1_config.proxy.https = "http://www-proxy.ericsson.se:8080"
    node1_config.vm.hostname = "node1.local"
    node1_config.ssh.forward_agent = true
    #node1_config.vm.provision "docker"
    node1_config.vm.provider "virtualbox" do |v|
      v.name = "shipyard-agent-node1"
      v.customize ["modifyvm", :id, "--memory", BOX_MEM]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end 
  end
  config.vm.define :node2 do |node2_config|
    node2_config.vm.box = BOX_NAME
    node2_config.vm.box_url = BOX_URI
    node2_config.vm.network :private_network, ip: "10.1.42.20"
	node2_config.proxy.http  = "http://www-proxy.ericsson.se:8080"
	node2_config.proxy.https = "http://www-proxy.ericsson.se:8080"
    node2_config.vm.hostname = "node2.local"
    node2_config.ssh.forward_agent = true
    node2_config.vm.provider "virtualbox" do |v|
      v.name = "shipyard-agent-node2"
      v.customize ["modifyvm", :id, "--memory", BOX_MEM]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end
  end

  config.vm.provision :hosts do |provisioner|
        provisioner.add_host '10.1.42.10', ['node1.local']
        provisioner.add_host '10.1.42.20', ['node2.local']
        provisioner.add_host '10.1.42.30', ['shipyardserver.local']
  end
  config.vm.provision :ansible do |ansible|
	    ansible.playbook = "./site.yml"
	    ansible.verbose =  'vvvv'	    
		ansible.groups = {
		  "shipyard-servers" => ["server1"],
		  "shipyard-agents" => ["node1", "node2"],
		  "all_groups:children" => ["shipyard-servers", "shipyard-agents"]		  		  
		}
		ansible.extra_vars = {
			shipyard_server_url: "http://shipyardserver.local:8000",
			is_vagrant_run: true
		}
  end

  #config.vm.provision "docker"

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider :virtualbox do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
end
