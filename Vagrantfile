# -*- mode: ruby -*-
# vi: set ft=ruby :

#require 'berkshelf/vagrant'

VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.omnibus.chef_version = "11.8.2"
  config.berkshelf.enabled = true

  config.vm.host_name = "squid"

  config.vm.box = "precise64"
  config.vm.box_url = "http://files.vagrantup.com/precise64.box"

  config.vm.network :private_network, ip: "192.168.71.2"

  config.vm.provision :chef_solo do |chef|
    
    chef.add_recipe('apt')
    chef.add_recipe('squid')
    chef.add_recipe('sysctl')
    chef.add_recipe('simple_iptables')
    
    chef.json = {
      :squid => {
      },
      :sysctl => {
        :params => {
          'net.ipv4.ip_forward' => 1
        }
      },
      :simple_iptables => {
        :rules => {
          :nat => [
            '-A POSTROUTING -o eth0 -j MASQUERADE',
            '-A PREROUTING -i eth1 -s 192.168.71.0/8 -p tcp --dport 80 -j REDIRECT --to-port 3128'
          ],
          :filter => [
            '-A FORWARD -i eth1 -j ACCEPT'
          ]
        }
      }
    }

  end
end
