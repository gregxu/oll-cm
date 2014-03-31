# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Local variables for easier-to-read config
  project = "webapp"

  #
  # These settings mostly only affect the first-run after a new install
  #

  # This is the default blank box.  Use it if you would like to start from scratch:
  #config.vm.box = "#{project}.20140317.base"
  #config.vm.box_url = "https://s3.amazonaws.com/ab-dev-docs/vagrant-boxes/centos-6.5-x64-20140317.box"

  # This box is fully provisioned, so should spin up much faster:
  config.vm.box = "#{project}.provisioned.20140319.base"
  config.vm.box_url = "https://s3.amazonaws.com/ab-dev-docs/vagrant-boxes/provisioned-centos-6.5-x64-20140319.box"

  # Vagrant provides its own copy of this key, but the config is left here in case you would like to override:
  #config.ssh.private_key_path = "~/.ssh/vagrant"

  #
  # Special parameters passed to Virtualbox
  #

  config.vm.provider "virtualbox" do
    |v|

    # Needed for performance.  See http://www.lefred.be/node/164
    v.customize ["modifyvm", :id, "--ioapic", "on"]

    # Provisioning works a lot better with more memory, so leave this high at lear during first boot
    v.customize ["modifyvm", :id, "--memory", "1024"]

  end

  #
  # These settings affect every boot of the VM
  #

  # use public dhcp address, not NAT
  #config.vm.network :public_network
  # Needed to use NFS
  # http://docs.vagrantup.com/v2/networking/private_network.html
  config.vm.network "private_network", ip: "172.16.42.42"

  # Forward a variety of web ports
  config.vm.network :forwarded_port, guest: 80,   host: 8080
  config.vm.network :forwarded_port, guest: 8088, host: 8088
  config.vm.network :forwarded_port, guest: 443,  host: 8443
  # And MySQL
  config.vm.network :forwarded_port, guest: 3306, host: 13306

  config.vm.hostname = "#{project}.dev"

  # New directory structure for unified Portal team.  Until production matches this directory structure, Ansible will
  # create symlinks to match deprecated production locations of /var/mb2, and so forth.
  if Vagrant::Util::Platform.windows? then
    #SMB sharing is still in alpha state and doesn't work properly.
    #config.vm.synced_folder "..", "/opt/rp", id: "rp", type: 'smb'
    config.vm.synced_folder "..", "/opt/rp", mount_options: ['dmode=777'], id: "rp"
  else
    config.vm.synced_folder "..", "/opt/rp", id: "rp", type: 'nfs'
  end

  #
  # Provisioning runs after every boot, too
  #

  config.vm.provision "shell" do |s|
    s.path = "bin/provision_vagrant.sh"
  end

end


