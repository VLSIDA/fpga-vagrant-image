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
  config.vm.box = "generic/ubuntu1804"

  # Disable automatic box update checking. If you disable this, then
  # boxes will only be checked for updates when the user runs
  # `vagrant box outdated`. This is not recommended.
  # config.vm.box_check_update = false

  # Create a forwarded port mapping which allows access to a specific port
  # within the machine from a port on the host machine. In the example below,
  # accessing "localhost:8080" will access port 80 on the guest machine.
  # NOTE: This will enable public access to the opened port
  # config.vm.network "forwarded_port", guest: 80, host: 8080

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
  # For macos/linux:
  #config.vm.synced_folder "data", "/vagrant_data"
  # For WSL:
  config.vm.synced_folder "/mnt/c/vagrant/fpga-data", "/vagrant_data"

  # Provider-specific configuration so you can fine-tune various
  # backing providers for Vagrant. These expose provider-specific options.
  # Example for VirtualBox:
  #
  config.vm.provider "virtualbox" do |vb|
      vb.name = "fpga"
  #   # Display the VirtualBox GUI when booting the machine
  #   vb.gui = true
  #
  #   # Customize the amount of memory on the VM:
  #   vb.memory = "1024"
      vb.memory = "8192"
      vb.cpus = "2"

      #vb.customize ["modifyvm", :id, "--usb", "on"]
      #vb.customize ["modifyvm", :id, "--usbehci", "on"]
      #vb.customize ["usbfilter", "add", "0",
      #  "--target", :id,
      #  "--name", "iCEBreaker",
      #  "--product", "iCEBreaker V1.0c"]
  end
  #
  # View the documentation for the provider you are using for more
  # information on available options.


  # Allow X11 forwarding
  config.ssh.forward_x11 = true
  config.ssh.forward_agent = true

  config.vm.provision "shell", run: "once",inline: <<-SHELL
    # Enable X11 forwarding
    echo "X11UseLocalhost no" >> /etc/ssh/sshd_config
  SHELL

  # Enable provisioning with a shell script. Additional provisioners such as
  # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
  # documentation for more information about their specific syntax and use.
  #config.vm.provision "file", run: "once", source: "screenrc", destination: "/home/vagrant/.screenrc"
  config.vm.provision "file", run: "once", source: "50-lattice-ftdi.rules", destination: "/tmp/50-lattice-ftdi.rules"
  config.vm.provision "file", run: "once", source: "53-lattice-ftdi.rules", destination: "/tmp/53-lattice-ftdi.rules"
  config.vm.provision "file", run: "once", source: "99-fomu.rules", destination: "/tmp/99-fomu.rules"

  # General Ubuntu deps
  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    DEBIAN_FRONTEND=noninteractive apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" upgrade 

    ### Dependencies ###
    # General tools for building etc.
    apt-get install --no-install-recommends -y build-essential git ssh vim gosu autoconf automake libtool bison flex wget
    # Interactive tools
    apt-get install -y --no-install-recommends emacs screen gdb
    # Use bash instead of dash
    rm /bin/sh && ln -s /bin/bash /bin/sh
    apt-get install --no-install-recommends -y  python3 python3-numpy python3-scipy
    # Needed by ngspice
    #apt-get install --no-install-recommends -y libxaw7-dev
    # X11 dev not used
    #apt-get install --no-install-recommends -y libx11-dev libcairo2-dev
    # CAD dependencies
    apt-get install --no-install-recommends -y libqt5widgets5 libqt5x11extras5 libqt5printsupport5 libqt5xml5 libqt5sql5 libqt5svg5
    wget -q -O /tmp/libpng12.deb http://mirrors.kernel.org/ubuntu/pool/main/libp/libpng/libpng12-0_1.2.54-1ubuntu1_amd64.deb \
    && dpkg -i /tmp/libpng12.deb \
    && rm /tmp/libpng12.deb

    ### Utilities ###
    # X11
    #apt-get install -y --no-install-recommends x11-xserver-utils
    # Network debug tools (can be removed to save space)
    #apt-get install --no-install-recommends -y iputils-ping net-tools lsof wget whois nmap telnet curl dnsutils tcpdump traceroute
    # Needed to run lmstat
    apt-get install -y --no-install-recommends lsb lsb-release lsb-core
  SHELL

  config.vm.provision "shell", run: "first", inline: <<-SHELL
    # Icestorm
    cd
    apt-get install -y --no-install-recommends libftdi1 libftdi1-dev pkg-config
    git clone https://github.com/cliffordwolf/icestorm.git
    cd icestorm
    make && make install

    # Nextpnr
    cd
    apt-get install -y --no-install-recommends libboost-all-dev cmake qt5-default libeigen3-dev tcl-dev csh \
        qtscript5-dev libglu1-mesa-dev freeglut3-dev mesa-common-dev libqt5opengl5-dev
    git clone https://github.com/YosysHQ/nextpnr
    cd nextpnr 
    cmake -DARCH=ice40 .
    make -j$(nproc)
    make install

    # Yosys
    cd
    apt-get install -y --no-install-recommends build-essential clang bison flex \
        	libreadline-dev gawk tcl-dev libffi-dev git \
        	graphviz xdot pkg-config python3 libboost-system-dev \
        	libboost-python-dev libboost-filesystem-dev
    git clone https://github.com/cliffordwolf/yosys.git
    cd yosys
    make 
    make install

    # STLINK
    apt-get install -y --no-install-recommends libusb-1.0-0-dev sdcc
    git clone https://github.com/texane/stlink
    cd stlink
    make release
    cd build/Release
    make install
    ldconfig

    add-apt-repository ppa:team-gcc-arm-embedded/ppa
    apt-get update
    apt-get install -y --no-install-recommends gcc-arm-embedded
    #apt-get install -y --no-install-recommends gcc-arm-none-eabi 

    apt-get install -y --no-install-recommends dfu-utils
    sudo groupadd plugdev
    sudo usermod -a -G plugdev vagrant

  mv /tmp/50-lattice-ftdi.rules /etc/udev/rules.d
  mv /tmp/53-lattice-ftdi.rules /etc/udev/rules.d
  mv /tmp/99-fomu.rules /etc/udev/rules.d

  SHELL
end
