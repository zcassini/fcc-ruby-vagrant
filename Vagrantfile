# encoding: utf-8

# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  #config.vm.box = "opscode-ubuntu-12.04_chef-11.4.0"
  #config.vm.box_url = "https://opscode-vm-bento.s3.amazonaws.com/vagrant/opscode_ubuntu-12.04_chef-11.4.0.box"
  config.vm.box = "minimal/trusty64"
  config.ssh.forward_agent = true

  # Configue the virtual machine to use 2GB of RAM
  # config.vm.provider :virtualbox do |vb|
  #   vb.customize ["modifyvm", :id, "--memory", "2048"]
  # end

  config.vm.network :forwarded_port, guest: 3000, host: 3030

  # from the python group:
  # Add the tty fix as mentioned in issue 1673 on vagrant repo
  # To avoid 'stdin is not a tty' messages
  # vagrant provisioning in shell runs bash -l
  config.vm.provision "fix-no-tty", type: "shell" do |s|
      s.privileged = false
      s.inline = "sudo sed -i '/tty/!s/mesg n/tty -s \\&\\& mesg n/' /root/.profile"
  end

  # Use Chef Solo to provision our virtual machine
  config.vm.provision :chef_solo do |chef|
    chef.version = "12.10.40"
    # chef.cookbooks_path = ["cookbooks"]
    chef.cookbooks_path = ["cookbooks"]

    chef.add_recipe "apt"
    chef.add_recipe "ruby_build"
    chef.add_recipe "ruby_rbenv::user"
    # chef.add_recipe "ruby_rbenv::vagrant"
    chef.add_recipe 'postgresql::server'
    chef.add_recipe 'git'
    chef.add_recipe 'nodejs'
    chef.add_recipe 'heroku-toolbelt'

    # Install Ruby 2.2.1 and Bundler
    chef.json = {
      rbenv: {
        user_installs: [{
          user: 'vagrant',
          rubies: ["2.2.1"],
          global: "2.2.1",
          gems: {
            "2.2.1" => [
              { name: "bundler" },
              { name: "rails"},
            ]
          }
        }]
      },
      :postgresql => {
        :config   => {
          :listen_addresses => "*",
          :port             => "5432"
        },
        :pg_hba   => [
          {
            :type   => "local",
            :db     => "postgres",
            :user   => "postgres",
            :addr   => nil,
            :method => "trust"
          },
          {
            :type   => "host",
            :db     => "all",
            :user   => "all",
            :addr   => "0.0.0.0/0",
            :method => "md5"
          },
          {
            :type   => "host",
            :db     => "all",
            :user   => "all",
            :addr   => "::1/0",
            :method => "md5"
          }
        ],
        :password => {
          :postgres => "password"
        }
      },
      :git        => {
        :prefix => "/usr/local"
      }
    }
  end

  # config.vm.provision :chef_solo do |chef|
  #   chef.version = "12.10.40"
  #   chef.cookbooks_path = ["cookbooks"]
  #   chef.add_recipe :apt
  #   chef.add_recipe 'ruby_build'
  #   chef.add_recipe 'ruby_rbenv::user'
  #   chef.add_recipe 'postgresql::server'
  #   chef.add_recipe 'sqlite'
  #   chef.add_recipe 'git'
  #   #chef.add_recipe "nodejs"
  #   chef.json = {
  #     :rbenv      => {
  #       :user_installs => [
  #         {
  #           :user   => "vagrant",
  #           :rubies => [
  #             "2.3.1"
  #           ],
  #           :global => "2.3.1"
  #         }
  #       ]
  #     },
  #     :postgresql => {
  #       :config   => {
  #         :listen_addresses => "*",
  #         :port             => "5432"
  #       },
  #       :pg_hba   => [
  #         {
  #           :type   => "local",
  #           :db     => "postgres",
  #           :user   => "postgres",
  #           :addr   => nil,
  #           :method => "trust"
  #         },
  #         {
  #           :type   => "host",
  #           :db     => "all",
  #           :user   => "all",
  #           :addr   => "0.0.0.0/0",
  #           :method => "md5"
  #         },
  #         {
  #           :type   => "host",
  #           :db     => "all",
  #           :user   => "all",
  #           :addr   => "::1/0",
  #           :method => "md5"
  #         }
  #       ],
  #       :password => {
  #         :postgres => "password"
  #       }
  #     },
  #     :git        => {
  #       :prefix => "/usr/local"
  #     }
  #   }
  # end
end
