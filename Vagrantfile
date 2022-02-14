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
  # boxes at https://vagrantcloud.com/search.
  config.vm.box = "bento/ubuntu-20.04"

  config.vm.hostname = "ubuntu"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  config.vm.network "forwarded_port", guest: 5432, host: 5436

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine and only allow access
  # via 127.0.0.1 to disable public access
  # config.vm.network "forwarded_port", guest: 80, host: 8080, host_ip: "127.0.0.1"

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

  # Enable provisioning with a shell script. Additional provisioners such as
  # Ansible, Chef, Docker, Puppet and Salt are also available. Please see the
  # documentation for more information about their specific syntax and use.
  config.vm.provision "shell", inline: <<-SHELL
    echo "Creating the file repository configuration"
    sudo echo "deb http://apt.postgresql.org/pub/repos/apt/ `lsb_release -cs`-pgdg main 8.4" | sudo tee /etc/apt/sources.list.d/postgresql.list > /dev/null

    echo "Importing PostgreSQL key"
    # wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    sudo apt-get install curl ca-certificates gnupg
    curl -fsSL https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/apt.postgresql.org.gpg

    echo "Updating package lists"
    sudo apt-get update

    echo "Installing postgres"
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -q -y postgresql-8.4 postgresql-client-8.4 postgresql-contrib-8.4

    echo "Creating a new user and the develpment database"
    sudo -u postgres psql postgres -c "CREATE USER myproject_dev WITH ENCRYPTED PASSWORD 'myproject_dev'"
    sudo -u postgres psql postgres -c "CREATE DATABASE myproject_dev OWNER myproject_dev"

    # Allow access from outside
    sudo echo "listen_addresses = '*'" >> /etc/postgresql/8.4/main/postgresql.conf

    # Edit to allow socket access, not just local unix access
    echo "Patching pg_hba.conf to change -> socket access"
    sudo echo "host all all 0.0.0.0/0 md5" >> /etc/postgresql/8.4/main/pg_hba.conf

    # Restart Postgres to apply all change
    sudo service postgresql restart
  SHELL
end
