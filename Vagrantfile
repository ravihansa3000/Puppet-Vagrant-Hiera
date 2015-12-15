# -*- mode: ruby -*-
# vi: set ft=ruby :


# Global properties for Puppet Vagrant setup

IP_ADDR = "172.17.8.20"

BASE_DOMAIN = "local.dev.wso2.org"

nodes = [
   { :hostname => 'dev-sandbox', :box => 'ubuntu/trusty64', :ram => '1500'},
]


Vagrant.configure(2) do |config|

   nodes.each do |node|

      config.vm.define node[:hostname] do |node_config|
         node_config.vm.box = node[:box]
         node_config.vm.host_name = node[:hostname] + '.' + BASE_DOMAIN
         node_config.vm.network :private_network, ip: IP_ADDR
         node_config.vm.synced_folder("puppet", "/puppet")
         memory = node[:ram] ? node[:ram] : 256;

         config.vm.provider :virtualbox do |vb|
            vb.check_guest_additions = false
            vb.functional_vboxsf = false
            vb.gui = false
            vb.customize [
               'modifyvm', :id,
               '--name', node[:hostname],
               # '--resize', '5120'
               '--memory', memory.to_s
         ]
         end
      end
   end

$script = <<EOF
   mkdir -p /etc/puppet/modules;
   if [ ! -d /etc/puppet/modules/stdlib ]; then
      puppet module install puppetlabs/stdlib
   fi
EOF

   config.vm.provision :shell do |shell|
      shell.inline = $script
   end

   config.vm.provision :puppet do |puppet|
      puppet.manifests_path = 'puppet/manifests'
      puppet.manifest_file = 'site.pp'
      puppet.module_path = ['/home/akila/Documents/WSO2/WSO2-GitHub/Puppet-Modules.git',
                            '/etc/puppet/modules']
      puppet.options = "--verbose --debug"
      puppet.hiera_config_path = 'puppet/hiera/hiera.yaml'
      puppet.working_directory = '/puppet'

      # custom facts provided to Puppet
      # turn on/off vm_type variable to see diffrent behaviour
      puppet.facter = {
          "environment"     => "dev",
          "vm_type"         => "vagrant",
          "product_name"    => "wso2am",
          "product_version" => "1.9.1",
          "product_profile" => "default"
      }
   end
end
