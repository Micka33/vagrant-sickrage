# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "ubuntu/trusty64"

  # Multiple machines can be defined within the same project Vagrantfile
  # using the config.vm.define method call.
  config.vm.define "sickrage" do |sickrage|

    # If you'd like to try the latest version of Docker:
    # First, check that your APT system can deal with https URLs:
    # the file /usr/lib/apt/methods/https should exist.
    # If it doesn't, you need to install the package apt-transport-https.
    sickrage.vm.provision "shell",  inline: <<SH
      [ -e /usr/lib/apt/methods/https ] || {
        apt-get -y update
        apt-get -y install apt-transport-https
      }
SH

    # Then, add the Docker repository key to your local keychain.
    sickrage.vm.provision "shell",  inline: <<SH
      sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
SH

    # Add the Docker repository to your apt sources list,
    # update and install the lxc-docker package.
    # You may receive a warning that the package isn't trusted.
    # Answer yes to continue installation.
    sickrage.vm.provision "shell",  inline: <<SH
      sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
      apt-get -y update
      apt-get -y install lxc-docker
SH

    # It is easiest to install Git on Linux using the preferred
    # package manager of your Linux distribution.
    # Debian/Ubuntu
    # $ apt-get install git
    sickrage.vm.provision "shell",  inline: <<SH
      apt-get -y install git
SH

    # See https://github.com/Micka33/docker-rtorrent
    # Install docker-rtorrent
    # Build the rtorrent image
    # Run rtorrent
    sickrage.vm.provision "shell",  inline: <<SH
      git clone --depth=1 https://github.com/Micka33/docker-sickrage.git
      cd docker-sickrage
      docker build --tag micka33/rtorrent .
      docker run --name rtorrent -d -p 0.0.0.0:63256:63256 micka33/rtorrent
SH



    # See https://github.com/Micka33/docker-sickrage
    # Install docker-sickrage
    # Build the sickrage image
    # Run the sickrage container
    sickrage.vm.provision "shell",  inline: <<SH
      git clone --depth=1 https://github.com/Micka33/docker-sickrage.git
      cd docker-sickrage
      docker build --tag micka33/sickrage .
      docker run --name sickrage -d -p 0.0.0.0:80:8081 --link rtorrent/rtorrent micka33/sickrage
SH


    sickrage.vm.network "forwarded_port", guest: 80, host: 8080
    sickrage.vm.network "forwarded_port", guest: 63256, host: 63256

  end
















  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # config.vm.network "forwarded_port", guest: 80, host: 8080

  # Create a private network, which allows host-only access to the machine
  # using a specific IP.
  # config.vm.network "private_network", ip: "192.168.33.10"

  # Create a public network, which generally matched to bridged network.
  # Bridged networks make the machine appear as another physical device on
  # your network.
  # config.vm.network "public_network"

  # If true, then any SSH connections made will enable agent forwarding.
  # Default value: false
  # config.ssh.forward_agent = true

  # Share an additional folder to the guest VM. The first argument is
  # the path on the host to the actual folder. The second argument is
  # the path on the guest to mount the folder. And the optional third
  # argument is a set of non-required options.
  # config.vm.synced_folder "../data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  # config.vm.provider "virtualbox" do |vb|
  #   # Don't boot with headless mode
  #   vb.gui = true
  #
  #   # Use VBoxManage to customize the VM. For example to change memory:
  #   vb.customize ["modifyvm", :id, "--memory", "1024"]
  # end
  #
  # View the documentation for the provider you're using for more
  # information on available options.

  # Enable provisioning with CFEngine. CFEngine Community packages are
  # automatically installed. For example, configure the host as a
  # policy server and optionally a policy file to run:
  #
  # config.vm.provision "cfengine" do |cf|
  #   cf.am_policy_hub = true
  #   # cf.run_file = "motd.cf"
  # end
  #
  # You can also configure and bootstrap a client to an existing
  # policy server:
  #
  # config.vm.provision "cfengine" do |cf|
  #   cf.policy_server_address = "10.0.2.15"
  # end

end
