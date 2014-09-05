# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

BOX_MEM = "1024"
BOX_NAME =  "precise64"
BOX_URI = "http://files.vagrantup.com/precise64.box"
HOSTS_NO_PROXY = "localhost,127.0.0.0/8,shipyardserver.local,node1.local,node2.local,registry1.local,jenkins-master.local,jenkins-slave.local"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
    config.vm.box = BOX_NAME
    config.vm.box_url = BOX_URI
	config.vm.box_download_insecure = true
  
  # The url from where the 'config.vm.box' box will be fetched if it
  # doesn't already exist on the user's system.
  # config.vm.box_url = "http://files.vagrantup.com/precise64.box"
  config.vm.define :server1 do |server1_config|
    server1_config.vm.network :private_network, ip: "10.1.42.30"
    server1_config.vm.network :forwarded_port, host: 45680, guest: 80
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
    node1_config.vm.network :private_network, ip: "10.1.42.10"
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
    node2_config.vm.network :private_network, ip: "10.1.42.20"
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

  config.vm.define :registry1 do |registry1_config|
    registry1_config.vm.network :private_network, ip: "10.1.42.40"
	registry1_config.proxy.http  = "http://www-proxy.ericsson.se:8080"
	registry1_config.proxy.https = "http://www-proxy.ericsson.se:8080"    
	registry1_config.proxy.no_proxy = HOSTS_NO_PROXY    
	registry1_config.vm.network :forwarded_port, host: 45681, guest: 80    
    registry1_config.vm.hostname = "registry1.local"
    registry1_config.ssh.forward_agent = true
    registry1_config.vm.provider "virtualbox" do |v|
      v.name = "docker-registry"
      v.customize ["modifyvm", :id, "--memory", BOX_MEM]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end
  end

  config.vm.define :jenkins_slave do |jenkins_slave_config|
    jenkins_slave_config.vm.network :private_network, ip: "10.1.42.60"
    jenkins_slave_config.vm.hostname = "jenkins-slave.local"    
    jenkins_slave_config.ssh.forward_agent = true
    jenkins_slave_config.vm.provider "virtualbox" do |v|
      v.name = "jenkins-slave"
      v.customize ["modifyvm", :id, "--memory", BOX_MEM]
      v.customize ["modifyvm", :id, "--ioapic", "on"]
      v.customize ["modifyvm", :id, "--cpus", "2"]
      v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
      v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    end
  end

  config.vm.define :jenkins_master do |jenkins_master_config|
    jenkins_master_config.vm.network :private_network, ip: "10.1.42.70"
    jenkins_master_config.vm.hostname = "jenkins-master.local"
    jenkins_master_config.vm.network :forwarded_port, host: 45683, guest: 80
    jenkins_master_config.ssh.forward_agent = true
    jenkins_master_config.vm.provider "virtualbox" do |v|
      v.name = "jenkins-master"
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
        provisioner.add_host '10.1.42.40', ['registry1.local']
        provisioner.add_host '10.1.42.60', ['jenkins-slave.local']
        provisioner.add_host '10.1.42.70', ['jenkins-master.local']
  end
  config.vm.provision :ansible do |ansible|
	    ansible.playbook = "./site.yml"
	    ansible.verbose =  'vvvv'
	    ansible.vault_password_file = "./vault_password"	    
		ansible.groups = {
		  "shipyard-servers" => ["server1"],
		  "shipyard-agents" => ["node1", "node2", "registry1"],
		  "docker-registry-servers" => ["registry1"],
		  "jenkins-slave-farm" => ["jenkins_slave"],
		  "jenkins-master" => ["jenkins_master"],
		  "all_groups:children" => ["shipyard-servers", "shipyard-agents", "docker-registry-servers", "jenkins-slave-farm", "jenkins-master"]		  		  
		}
		ansible.extra_vars = {
			# Please note the shipyard_server_url tries a dns lookup and 
			# fails when running in vagrant mode hence using ip instead.
			shipyard_server_url: "http://10.1.42.30:8000",			
			no_proxy: HOSTS_NO_PROXY,
			is_dev_mode: true
		}
  end

end