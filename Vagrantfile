# -*- mode: ruby -*-
# vi: set ft=ruby :

# require YAML module
require 'yaml'

# load server config from YAML file
CONFIGURATION_YAML = YAML.load_file('config.yaml')
$puppet_lib_install_script = <<EOF
   mkdir -p /etc/puppet/modules;
   if [ ! -d /etc/puppet/modules/stdlib ]; then
      puppet module install puppetlabs/stdlib
   fi
   if [ ! -d /etc/puppet/modules/java ]; then
      puppet module install 7terminals-java
   fi
EOF

Vagrant.configure(2) do |config|

	CONFIGURATION_YAML["servers"].each do |server|
		if (defined?(server['enabled']) && server['enabled'] != false)
      puts "Provisioning VirtualBox with hostname: ", server['hostname']
			config.vm.define server["hostname"] do |server_config|
				server_config.vm.box = server["box"]
				server_config.vm.host_name = server["hostname"]
				server_config.vm.network :private_network, ip: server["ip"]
				server_config.vm.synced_folder("/etc/puppet/files", "/puppet-files")
        server_config.vm.synced_folder(CONFIGURATION_YAML["puppet"]["hiera_path"], "/puppet-hiera")
				memory = server["ram"] ? server["ram"] : 256;

				server_config.vm.provider :virtualbox do |vb|
					vb.name = server["hostname"]
					vb.check_guest_additions = false
					vb.functional_vboxsf = false
					vb.gui = false
					vb.customize ["modifyvm", :id, "--groups", "/WSO2 Puppet Development"]
					vb.customize ["modifyvm", :id, "--memory", server["ram"]]
					vb.customize ["modifyvm", :id, "--cpus", server["cpu"]]
				end

				server_config.vm.provision :shell do |shell|
					shell.inline = $puppet_lib_install_script
				end

				server_config.vm.provision :puppet do |puppet|
					puppet.manifests_path = CONFIGURATION_YAML["puppet"]["manifests_path"]
					puppet.manifest_file = 'site.pp'
					puppet.module_path = CONFIGURATION_YAML["puppet"]["module_path"]
					puppet.options = "--verbose --debug --fileserverconfig=/vagrant/fileserver.conf"
					puppet.hiera_config_path = CONFIGURATION_YAML["puppet"]["hiera_config_path"]
					puppet.working_directory = CONFIGURATION_YAML["puppet"]["working_directory"]

					# custom facts provided to Puppet
					# turn on/off vm_type variable to see diffrent behaviour
					puppet.facter = server["facters"]
				end
			end
		end
	end
end
