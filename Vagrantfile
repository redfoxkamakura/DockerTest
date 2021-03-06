#############################
# Provision Scripts
#############################
$app_update = <<SCRIPT
#!/bin/bash

sudo apt-get update
sudo apt-get -y upgrade

SCRIPT

$install_docker = <<SCRIPT

sudo apt-get -y install docker.io
sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker
sudo sed -i '$acomplete -F _docker docker' /etc/bash_completion.d/docker.io
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys 36A1D7869245C8950F966E92D8576A8BA88D21E9
sudo sh -c "echo deb https://get.docker.io/ubuntu docker main\
> /etc/apt/sources.list.d/docker.list"
sudo apt-get update
sudo apt-get -y install lxc-docker

# nsenter install
#  container login tool
#  see http://jpetazzo.github.io/2014/03/23/lxc-attach-nsinit-nsenter-docker-0-9/
cd /home/vagrant && git clone git://git.kernel.org/pub/scm/utils/util-linux/util-linux.git util-linux
sudo apt-get install -y libncurses5-dev libslang2-dev gettext zlib1g-dev libselinux1-dev debhelper lsb-release pkg-config po-debconf autoconf automake autopoint libtool python-dev
cd /home/vagrant/util-linux && ./autogen.sh && ./configure && sudo make
sudo cp /home/vagrant/util-linux/nsenter /usr/local/bin
# use
# PID=$(docker inspect --format '{{.State.Pid}}' my_container_id)
# nsenter --target $PID --mount --uts --ipc --net --pid

SCRIPT

#############################
# Vagrantfile
#############################
Vagrant.configure(2) do |config|

  # Box
  config.vm.box_url = "https://cloud-images.ubuntu.com/vagrant/trusty/current/trusty-server-cloudimg-amd64-vagrant-disk1.box"
  config.vm.box = "ubuntu14.04"

  ############################
  # web node
  ############################
  config.vm.define "web" do |web|

    # SSH Config
    web.ssh.forward_agent = true
    web.ssh.shell = "bash -c 'BASH_ENV=/etc/profile exec bash'"

    # Network
    for port in 10000..10100 do
      web.vm.network "forwarded_port", guest: port, host: port
    end
    web.vm.network "private_network", ip: "192.168.33.10"

    # Synced_folder
    # web.vm.synced_folder ".", "/home/vagrant/"

    # VM Config
    web.vm.provider "virtualbox" do |vb|
      vb.name   = "web"
      vb.memory = 2024
    end

   # Provisioning
   web.vm.provision "shell", :inline => $app_update
   web.vm.provision "shell", :inline => $install_docker

  end

end
