# -*- mode: ruby -*-
# vi: set ft=ruby :

# require YAML module
require 'yaml'

# load server config from YAML file
servers = YAML.load_file('servers.yaml')

Vagrant.configure(2) do |config|

 servers.each do |server|

    config.vm.define server["hostname"] do |server_config|
       server_config.vm.box = server["box"]
       server_config.vm.host_name = server["hostname"]
       server_config.vm.network :private_network, ip: server["ip"]
       server_config.vm.synced_folder("/etc/puppet/files", "/puppet-files")
       memory = server["ram"] ? server["ram"] : 256;

       server_config.vm.provider :virtualbox do |vb|
	  vb.name = server["hostname"]
          vb.check_guest_additions = false
          vb.functional_vboxsf = false
          vb.gui = false
	  vb.customize ["modifyvm", :id, "--groups", "/WSO2 Development"]
          vb.customize ["modifyvm", :id, "--memory", server["ram"]]
          vb.customize ["modifyvm", :id, "--cpus", server["cpu"]]
       end

$script = <<EOF
   mkdir -p /etc/puppet/modules;
   if [ ! -d /etc/puppet/modules/stdlib ]; then
      puppet module install puppetlabs/stdlib
   fi
   if [ ! -d /etc/puppet/modules/java ]; then
      puppet module install 7terminals-java
   fi
EOF

      server_config.vm.provision :shell do |shell|
         shell.inline = $script
      end

      server_config.vm.provision :puppet do |puppet|
         puppet.manifests_path = 'puppet/manifests'
         puppet.manifest_file = 'site.pp'
         puppet.module_path = ['/home/rajkumar/projects/wso2/public/puppet-modules',
                            '/etc/puppet/modules']
         puppet.options = "--verbose --debug --fileserverconfig=/vagrant/fileserver.conf"
         puppet.hiera_config_path = 'puppet/hiera/hiera.yaml'
         puppet.working_directory = '/vagrant/puppet'

         # custom facts provided to Puppet
         # turn on/off vm_type variable to see diffrent behaviour
         puppet.facter = {
             "environment"     => server["environment"],
             "vm_type"         => server["vm_type"],
             "product_name"    => server["product_name"],
             "product_version" => server["product_version"],
             "product_profile" => server["product_profile"]
         }
      end
    end
 end 
end
