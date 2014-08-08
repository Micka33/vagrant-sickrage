# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'fileutils'

Vagrant.require_version ">= 1.6.0"

CLOUD_CONFIG_PATH = File.join(File.dirname(__FILE__), "user-data")
CONFIG = File.join(File.dirname(__FILE__), "config.rb")
# Defaults for config options defined in CONFIG
$num_instances = 1
$update_channel = "alpha"
$enable_serial_logging = false
$vb_gui = true
$vb_memory = 1024
$vb_cpus = 1
if File.exist?(CONFIG)
  require CONFIG
end

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"


Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|

  # Every Vagrant virtual environment requires a box to build off of.
  config.vm.box = "coreos-%s" % $update_channel
  config.vm.box_version = ">= 308.0.1"
  config.vm.box_url = "http://%s.release.core-os.net/amd64-usr/current/coreos_production_vagrant.json" % $update_channel

  # Limitate the resources used by our VMs
  config.vm.provider "virtualbox" do |v|
    # On VirtualBox, we don't have guest additions or a functional vboxsf
    # in CoreOS, so tell Vagrant that so it can be smarter.
    v.check_guest_additions = false
    v.functional_vboxsf = false
  end
  # plugin conflict
  if Vagrant.has_plugin?("vagrant-vbguest") then
    config.vbguest.auto_update = false
  end


  # Multiple machines can be defined within the same project Vagrantfile
  # using the config.vm.define method call.
  config.vm.define vm_name = "coreos" do |coreos|
    coreos.vm.hostname = vm_name

    coreos.vm.provider :virtualbox do |vb, override|
      vb.gui = $vb_gui
      vb.memory = $vb_memory
      vb.cpus = $vb_cpus
      vb.customize ["modifyvm", :id, "--cpuexecutioncap", "70"]
    end
    if $enable_serial_logging
      logdir = File.join(File.dirname(__FILE__), "log")
      FileUtils.mkdir_p(logdir)
      serialFile = File.join(logdir, "%s-serial.txt" % vm_name)
      FileUtils.touch(serialFile)
      coreos.vm.provider :vmware_fusion do |v, override|
        v.vmx["serial0.present"] = "TRUE"
        v.vmx["serial0.fileType"] = "file"
        v.vmx["serial0.fileName"] = serialFile
        v.vmx["serial0.tryNoRxLoss"] = "FALSE"
      end
      coreos.vm.provider :virtualbox do |vb, override|
        vb.customize ["modifyvm", :id, "--uart1", "0x3F8", "4"]
        vb.customize ["modifyvm", :id, "--uartmode1", serialFile]
      end
    end
    # Uncomment below to enable NFS for sharing the host machine into the coreos-vagrant VM.
    coreos.vm.network :private_network, ip: '172.17.8.11'
    config.vm.synced_folder ".", "/home/core/share", id: "core", :nfs => true, :mount_options => ['nolock,vers=3,udp']

    coreos.vm.network "forwarded_port", guest: 80, host: 8080
    coreos.vm.network "forwarded_port", guest: 63256, host: 63256


    if File.exist?(CLOUD_CONFIG_PATH)
      coreos.vm.provision :file, :source => "#{CLOUD_CONFIG_PATH}", :destination => "/tmp/vagrantfile-user-data"
      coreos.vm.provision :shell, :inline => "mv /tmp/vagrantfile-user-data /var/lib/coreos-vagrant/", :privileged => true
    end






    # coreos.vm.provision "shell",  inline: <<SH
    #   # If you'd like to try the latest version of Docker:
    #   # First, check that your APT system can deal with https URLs:
    #   # the file /usr/lib/apt/methods/https should exist.
    #   # If it doesn't, you need to install the package apt-transport-https.
    #   [ -e /usr/lib/apt/methods/https ] || {
    #     apt-get -y update
    #     apt-get -y install apt-transport-https
    #   }
    #   # Then, add the Docker repository key to your local keychain.
  		# sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
    #   # Add the Docker repository to your apt sources list,
    #   # update and install the lxc-docker package.
    #   # You may receive a warning that the package isn't trusted.
    #   # Answer yes to continue installation.
    #   sh -c "echo deb https://get.docker.io/ubuntu docker main > /etc/apt/sources.list.d/docker.list"
    #   apt-get -y update
    #   apt-get -y install lxc-docker
    #   # It is easiest to install Git on Linux using the preferred
    #   # package manager of your Linux distribution.
    #   # Debian/Ubuntu
    #   # $ apt-get install git
    #   apt-get -y install git
# SH

#     coreos.vm.provision "shell",  inline: <<SH
# 	    # See https://github.com/Micka33/docker-rtorrent
# 	    # Install docker-rtorrent
# 	    # Build the rtorrent image
# 	    # Run rtorrent
#       git clone --depth=1 https://github.com/Micka33/docker-sickrage.git
#       cd docker-sickrage
#       docker build --tag micka33/rtorrent .
#       docker run --name rtorrent -d -p 0.0.0.0:63256:63256 micka33/rtorrent
# 	    # See https://github.com/Micka33/docker-sickrage
# 	    # Install docker-sickrage
# 	    # Build the sickrage image
# 	    # Run the sickrage container
#       git clone --depth=1 https://github.com/Micka33/docker-sickrage.git
#       cd docker-sickrage
#       docker build --tag micka33/sickrage .
#       docker run --name sickrage -d -p 0.0.0.0:80:8081 --link rtorrent/rtorrent micka33/sickrage
# SH

  end













end
