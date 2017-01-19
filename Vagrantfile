# -*- mode: ruby -*-
# vi: set ft=ruby :

st2user     = ENV['ST2USER'] ? ENV['ST2USER'] : 'st2admin'
st2passwd   = ENV['ST2PASSWORD'] ? ENV['ST2PASSWORD'] : 'Ch@ngeMe'
install     = ENV['INSTALL_ST2'] ? ENV['INSTALL_ST2'] : 'yes'
verify      = ENV['VERIFY_ST2'] ? ENV['VERIFY_ST2'] : 'yes'

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

VIRTUAL_MACHINES = {
  :trusty => {
    :hostname => 'st2-packages-trusty',
    :box => 'ubuntu/trusty64',
    :ip => '192.168.16.20',
  },
  :xenial => {
    :hostname => 'st2-packages-xenial',
    :box => 'ubuntu/xenial64',
    :ip => '192.168.16.21',
  },
  :el7 => {
    :hostname => 'st2-packages-el7',
    :box => 'centos/7',
    :ip => '192.168.16.22',
  },
}

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  VIRTUAL_MACHINES.each do |name, cfg|
    config.vm.define name do |vm_config|
      vm_config.vm.hostname = cfg[:hostname]
      vm_config.vm.box = cfg[:box]

      # Box Specifications
      vm_config.vm.provider :virtualbox do |vb|
        vb.name = "#{cfg[:hostname]}"
        # NOTE: With 2048MB, system slows due to kswapd. Recommend at least 4096MB.
        vb.customize ['modifyvm', :id, '--memory', '4096']
        vb.cpus = 2
      end

      # Sync folder using NFS
      vm_config.vm.synced_folder '.', '/vagrant', nfs: true

      # Configure a private network
      vm_config.vm.network "private_network", ip: "#{cfg[:ip]}"

      # Public (bridged) network may come handy for external access to VM (e.g. sensor development)
      # See https://www.vagrantup.com/docs/networking/public_network.html
      # st2.vm.network "public_network", bridge: 'en0: Wi-Fi (AirPort)'

      # Install docker-engine
      vm_config.vm.provision :docker

      vm_config.vm.provision :shell, :path => "scripts/setup-vagrant.sh", :privileged => false, :args => ["#{name}", "#{st2user}", "#{st2passwd}", "#{install}", "#{verify}"]
    end
  end
end
