# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  # All Vagrant configuration is done here. The most common configuration
  # options are documented and commented below. For a complete reference,
  # please see the online documentation at vagrantup.com.

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "hashicorp/precise64"

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  config.vm.network :forwarded_port, guest: 80, host: 6001
  config.vm.network :forwarded_port, guest: 3000, host: 6002

  config.vm.define "api", primary: true do |api|
    api.vm.synced_folder ".", "/usr/local/deckbrew"
    api.vm.provision "ansible" do |ansible|
      ansible.playbook = "provisioning/deckbrew.yml"
      ansible.verbose = 'vv'
      ansible.extra_vars = {
        deckbrew: {
          db: {
            user: ENV["DATABASE_USER"],
            password: ENV["DATABASE_PASSWORD"],
          },
          hostname: "http://localhost:6001",
        }
      }
    end
    #          "event" => "vagrant-ready",
  end

  config.vm.define "image" do |image|
    image.vm.provision :chef_solo do |chef|
      chef.cookbooks_path = "cookbooks"
      chef.add_recipe "deckbrew::image"
    end
  end
end
