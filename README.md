# Puppet Vagrant

A vagrant for setting up WSO2 servers using puppet and hiera. Originally created by [Akila Ravihansa] (https://github.com/ravihansa3000).

## How to use

1. Clone puppet vagrant git repository and consider this path in local machine as puppet-vagrant-home:

    ````
    git clone https://github.com/imesh/puppet-vagrant.git
    ````
2. Clone WSO2 puppet modules git repository and consider this path in local machine as puppet-home:

    ````
    git clone https://github.com/wso2/puppet-modules.git
    ````
3. Update puppet-home path in [puppet-vagrant-home]/config.yaml file.
4. Download and copy Oracle JDK 1.7 distribution to the following path:

    ````
    [puppet-home]/modules/wso2base/files/jdk-7u80-linux-x64.tar.gz
    ````
5. Download and copy required WSO2 product distributions to each files folder:

    ````
    [puppet-home]/modules/wso2esb/files
    [puppet-home]/modules/wso2am/files
    [puppet-home]/modules/wso2as/files
    ````
6. Update [puppet-vagrant-home]/config.yaml with required products and VM configurations (cpu, memory).
7. Update [puppet-home]/hieradata with required product configurations.
8. Execute the following command to start the VMs:

    ````
    vagrant up
    ````
