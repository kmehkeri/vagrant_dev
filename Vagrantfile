# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure(2) do |config|
  # Base box
  config.vm.box = "kaorimatz/ubuntu-16.04-amd64"
  config.vm.box_check_update = false

  # Network configuration
  config.vm.network :forwarded_port, guest: 3000, host: 3000 # RoR
  config.vm.network :forwarded_port, guest: 8080, host: 8080 # Web

  # Provider-specific configuration
  config.vm.provider "virtualbox" do |vb|
    vb.name = "vagrant_dev"
    vb.cpus = 2
    vb.memory = "4096"
    vb.gui = true
  end

  # System provisioning
  config.vm.provision "shell", inline: <<-EOF
    # Add software sources
    apt-get -y update
    apt-get -y install apt-transport-https ca-certificates
    # * SBT
    echo "deb https://dl.bintray.com/sbt/debian /" >/etc/apt/sources.list.d/sbt.list
    apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 642AC823
    # * Docker
    echo "deb https://apt.dockerproject.org/repo ubuntu-xenial main" >/etc/apt/sources.list.d/docker.list
    apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D

    # Preconfiguration for MySQL
    debconf-set-selections <<< 'mysql-server mysql-server/root_password password vagrant'
    debconf-set-selections <<< 'mysql-server mysql-server/root_password_again password vagrant'

    # Install software
    apt-get -y update
    apt-get -y autoremove
    apt-get -y install docker-engine \
                       git \
                       httpie \
                       libmysqlclient-dev \
                       linux-image-extra-$(uname -r) \
                       linux-image-extra-virtual \
                       mercurial \
                       mysql-server \
                       scala \
                       sbt \
                       ruby \
                       guake \
                       vim \
                       xfce4
    
    # Post-config
    usermod -aG docker vagrant
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

    # Install VIM Scala plugin
    mkdir -p ~/.vim/{ftdetect,indent,syntax} && for d in ftdetect indent syntax ; do wget -O ~/.vim/$d/scala.vim https://raw.githubusercontent.com/derekwyatt/vim-scala/master/$d/scala.vim; done

    # Git setup
    git config --global credential.helper "cache --timeout=86400"
  EOF
end
