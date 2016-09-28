# -*- mode: ruby -*-
# vi: set ft=ruby :
# Create a linux VM to use as a personal development environment

@install_docker = <<SCRIPT
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

@install_nodejs = <<SCRIPT
#!/bin/bash
yum install -y epel-release git
yum install -y nodejs npm
npm install -g bower grunt-cli
SCRIPT

@install_ruby = <<SCRIPT
#!/bin/bash
# see http://rvm.io/
gpg --keyserver hkp://keys.gnupg.net --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3
curl -sSL https://get.rvm.io | bash -s stable
source /usr/local/rvm/scripts/rvm
rvm install 2.3.1
gem install bundler
SCRIPT

@install_jekyll = <<SCRIPT
#!/bin/bash
gem install jekyll
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
  config.vm.network 'forwarded_port', guest: 9100, host: 9100
  config.vm.network 'forwarded_port', guest: 35_730, host: 35_730

  # config.vm.provision 'shell', inline: @install_docker
  config.vm.provision 'shell', inline: @install_nodejs
  config.vm.provision 'shell', inline: @install_ruby
  config.vm.provision 'shell', inline: @install_jekyll
end
