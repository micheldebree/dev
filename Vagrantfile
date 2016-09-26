# -*- mode: ruby -*-
# vi: set ft=ruby :
install_docker = <<SCRIPT
#!/bin/bash

# add docker repo
sudo tee /etc/yum.repos.d/docker.repo <<-'EOF'
[dockerrepo]
name=Docker Repository
baseurl=https://yum.dockerproject.org/repo/main/centos/$releasever/
enabled=1
gpgcheck=1
gpgkey=https://yum.dockerproject.org/gpg
EOF

# install
sudo yum install -y docker-engine

# to be able to use docker without sudo
sudo usermod -aG docker vagrant

# install and start docker daemon as a service
sudo chkconfig docker on
sudo systemctl start docker
SCRIPT

install_vicar = <<SCRIPT
  # build vicar docker image
  cd /vagrant/vicar
  docker build -t micheldebree/vicar-dev .

  # create a new container of the image
  docker create --name vicar-dev -p 4100:4100 -p 35730:35730 -v $PWD:$PWD micheldebree/vicar-dev /bin/bash -c "cd $PWD; grunt serve"
SCRIPT

install_website = <<SCRIPT
  # build vicar docker image
  cd /vagrant/micheldebree.github.io
  docker build -t micheldebree/website-dev .

  # create a new instance of the image
  docker create --name website-dev -p 4000:4000 -v $PWD:$PWD micheldebree/website-dev /bin/bash -c "cd $PWD; jekyll serve --incremental --watch --force_polling"
SCRIPT

start_vicar = <<SCRIPT
  docker start vicar-dev
SCRIPT

start_website = <<SCRIPT
  docker start website-dev
SCRIPT

Vagrant.configure(2) do |config|
  config.vm.provider 'virtualbox' do |v|
    v.name = 'Michel de Bree DEV'
    v.memory = 2048
  end
  config.vm.box = 'bento/centos-7.1'
  config.vm.hostname = 'michel-dev'

  # workaround for https://github.com/mitchellh/vagrant/issues/7610
  config.ssh.insert_key = false

  config.vm.network 'forwarded_port', guest: 4000, host: 4000
  config.vm.network 'forwarded_port', guest: 4100, host: 4100
  config.vm.network 'forwarded_port', guest: 35_730, host: 35_730

  config.vm.provision 'shell', inline: install_docker
  config.vm.provision 'shell', inline: install_vicar
  config.vm.provision 'shell', inline: install_website
end
