# Vagrantfile



#Required plugins
require 'vagrant-openstack-provider'
require 'inifile'
require 'fileutils'
require 'yaml'

Vagrant.configure(2) do |config|

# Load configuration YAML file
  if (File.exist?('config.yml') == true )
   vmconfig = YAML.load_file('config.yml')
  end

# Load instances yaml file
  if (File.exist?('instances.yml') == true )
   instances  = YAML.load_file('instances.yml')
  end

  config.env.enable
  config.ssh.insert_key = false

  config.vm.provider "openstack" do |os, override|
      # Configure the OpenStack provider for Vagrant
	    override.ssh.pty = true

      # Specify the default SSH username and private key
	    override.ssh.username = vmconfig['openstack_remote_user']
	    override.ssh.private_key_path = vmconfig['private_key_path']
      	    #security_groups = vmconfig['security_groups']

      # Specify OpenStack authentication information
	    os.username = ENV['OS_USERNAME']
	    os.password = ENV['OS_PASSWORD']
	    os.openstack_auth_url = vmconfig['auth_url']
	    os.tenant_name = vmconfig['tenant_name']
	    os.openstack_network_url = vmconfig['network_url']

      # Specify instance information
      	    #os.server_name = vmconfig['server']
	    os.flavor = vmconfig['flavor']
	    os.image = vmconfig['image']
	    os.networks = vmconfig['networks']
	    os.keypair_name = vmconfig['keypair_name']
           #os.security_groups =  vmconfig['security_groups']
	    os.floating_ip_pool = vmconfig['floating_ip_pool']
  end

  instances['nodes'].each_with_index do |instance, index|
  	  config.vm.define instance do |s|
	       #read server name from instances.yml.default file
	       s.vm.provider :openstack do |os, override|
		    os.server_name = instance
       	       end

		 # Run ansible after the last node comes up
 		if instance == instances['nodes'].last
  			s.vm.provision "shell", inline: "true", preserve_order: true
  			s.vm.provision "ansible" do |ansible|
 			  	ansible.playbook = "nginx.yml"
  	        		ansible.limit='all'
     			 	ansible.groups = {'nodes' => instances['nodes'] }
  			end
    	   	end
  	 end
   end
end
