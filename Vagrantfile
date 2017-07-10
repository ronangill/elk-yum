# -*- mode: ruby -*-
# vi: set ft=ruby :

# All Vagrant configuration is done below. The "2" in Vagrant.configure
# configures the configuration version (we support older styles for
# backwards compatibility). Please don't change it unless you know what
# you're doing.
Vagrant.configure("2") do |config|
  # The most common configuration options are documented and commented below.
  # For a complete reference, please see the online documentation at
  # https://docs.vagrantup.com.

  # Every Vagrant development environment requires a box. You can search for
  # boxes at https://atlas.hashicorp.com/search.
  config.vm.box = "centos/7"

  config.vm.provider :virtualbox do |v|
    v.customize ["modifyvm", :id, "--memory", 4096]
  end

  config.vm.network "private_network", ip: "192.168.33.10", virtualbox__intnet: true
  config.vm.network "forwarded_port", guest: 9200, host: 9200
  config.vm.network "forwarded_port", guest: 5601, host: 5601
  config.vm.provision "file", source: "elasticsearch.repo", destination: "/tmp/elasticsearch.repo"
  config.vm.provision "file", source: "kibana.repo", destination: "/tmp/kibana.repo"
  config.vm.provision "file", source: "logstash.repo", destination: "/tmp/logstash.repo"

  config.vm.provision "shell", inline: <<-SHELL
    sudo yum install net-tools
    BASE_URL=http://download.oracle.com/otn-pub/java/jdk
    JAVA_URL="$BASE_URL/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-x64.rpm"
    curl -L -C - -b "oraclelicense=accept-securebackup-cookie" -O "${JAVA_URL}"
    sudo yum -y localinstall jdk-8u131-linux-x64.rpm

    sudo rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch

    sudo mv /tmp/*.repo /etc/yum.repos.d/
    echo 'yum install components'
    sudo yum -y install elasticsearch kibana logstash

    echo 'network.host: 0.0.0.0' | sudo tee -a /etc/elasticsearch/elasticsearch.yml
    echo 'http.host: 0.0.0.0' | sudo tee -a /etc/logstash/logstash.yml
    echo 'server.host: 0.0.0.0' | sudo tee -a /etc/kibana/kibana.yml

    echo 'Create unix services'
    sudo /bin/systemctl enable elasticsearch
    sudo /bin/systemctl enable kibana
    sudo /bin/systemctl enable logstash

    sudo systemctl start kibana
    sudo systemctl start elasticsearch
    sudo systemctl start logstash
  SHELL

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
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
  # end
  #
  # View the documentation for the provider you are using for more
  # information on available options.

  # Define a Vagrant Push strategy for pushing to Atlas. Other push strategies
  # such as FTP and Heroku are also available. See the documentation at
  # https://docs.vagrantup.com/v2/push/atlas.html for more information.
  # config.push.define "atlas" do |push|
  #   push.app = "YOUR_ATLAS_USERNAME/YOUR_APPLICATION_NAME"
  # end
end
