#require 'berkshelf/vagrant'

Vagrant::Config.run do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # The path to the Berksfile to use with Vagrant Berkshelf
  # config.berkshelf.berksfile_path = "./Berksfile"

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.only = []

  # An array of symbols representing groups of cookbook described in the Vagrantfile
  # to skip installing and copying to Vagrant's shelf.
  # config.berkshelf.except = []

  #config.vm.host_name = "squid-berkshelf"
  config.vm.host_name = "squid"

  config.vm.box = "precise32"
  config.vm.box_url = "http://files.vagrantup.com/precise32.box"
#  config.vm.box = "opscode-centos-6.3"
#  config.vm.box_url = "https://dl.dropbox.com/u/31081437/Berkshelf-CentOS-6.3-x86_64-minimal.box"

  # Boot with a GUI so you can see the screen. (Default is headless)
  # config.vm.boot_mode = :gui

  # Assign this VM to a host-only network IP, allowing you to access it
  # via the IP. Host-only networks can talk to the host machine as well as
  # any other machines on the same network, but cannot be accessed (through this
  # network interface) by any external networks.
  config.vm.network :hostonly, "10.0.0.10"

  # Assign this VM to a bridged network, allowing you to connect directly to a
  # network using the host's network device. This makes the VM appear as another
  # physical device on your network.

  # config.vm.network :bridged

  # Forward a port from the guest to the host, which allows for outside
  # computers to access the VM, whereas host only networking does not.
  # config.vm.forward_port 80, 8080
  config.vm.forward_port 3128, 3128

  # Share an additional folder to the guest VM. The first argument is
  # an identifier, the second is the path on the guest to mount the
  # folder, and the third is the path on the host to the actual folder.
  # config.vm.share_folder "v-data", "/vagrant_data", "../data"

  config.ssh.max_tries = 40
  config.ssh.timeout   = 120

  vm_cookbooks_path = "/usr/local/chef_managed_cookbooks"
  vm_site_cookbooks_path = "/usr/local/chef_site_cookbooks"
  cmd = "
  mkdir -p #{vm_site_cookbooks_path}/cookbooks
  rm   #{vm_site_cookbooks_path}/cookbooks/squid
  ln -s /vagrant #{vm_site_cookbooks_path}/cookbooks/squid

  gem install librarian-chef
  mkdir -p #{vm_cookbooks_path}/cookbooks
  cp -fr /vagrant/Cheffile #{vm_cookbooks_path}
  cd #{vm_cookbooks_path}
  librarian-chef install
  "
#  print(cmd)  
  config.vm.provision :shell, :inline => cmd


  config.vm.provision :chef_solo do |chef|
    chef.json = {
      :mysql => {
        :server_root_password => 'rootpass',
        :server_debian_password => 'debpass',
        :server_repl_password => 'replpass'
      },
      :squid => {
        :authentication => 'basic',  # squid/squid
      },
    }

    chef.cookbooks_path = [
#                     "cookbooks", 
          [:vm, vm_cookbooks_path],
          [:vm, vm_site_cookbooks_path],
        ]
    chef.run_list = [
      "recipe[apt::default]",
      "recipe[squid::default]",
    ]
  end
end
