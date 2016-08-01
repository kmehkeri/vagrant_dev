# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  # Base box
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false

  # Network configuration
  config.vm.network :forwarded_port, guest: 3000, host: 3000 # RoR

  # Provider-specific configuration
  config.vm.provider "virtualbox" do |vb|
    vb.name = "vagrant_dev"
    vb.cpus = 2
    vb.memory = "2048"
    vb.gui = true
  end

  # System provisioning
  config.vm.provision "shell", inline: <<-EOF
    # Add software sources (SBT)
    sudo add-apt-repository "deb https://dl.bintray.com/sbt/debian /"
    apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823

    # Preconfiguration for MySQL
    debconf-set-selections <<< 'mysql-server mysql-server/root_password password vagrant'
    debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password vagrant'

    # Install software
    apt-get -y update
    apt-get -y install git mercurial mysql-server scala sbt ruby guake xfce4
  EOF

  # User provisioning
  config.vm.provision "shell", privileged: false, inline: <<-EOF
    # Install dotfiles
    git clone https://github.com/kmehkeri/dotfiles.git ~/dotfiles
    ~/dotfiles/install.sh
    rm -f ~/{.bashrc.old,.bash_profile.old,.vimrc.old}

    # Install RVM
    gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
    curl -sSL https://get.rvm.io | bash -s stable --ruby
  EOF
end
