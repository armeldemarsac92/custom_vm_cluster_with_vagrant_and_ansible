Vagrant.configure("2") do |config|

  config.vm.define "dhcp_server" do |dhcp_server|

    dhcp_server.vm.box = "tvlooy/openbsd-7.4-amd64"
    dhcp_server.vm.box_version = "20231929"

    dhcp_server.ssh.shell = "/bin/ksh"

    dhcp_server.vm.synced_folder ".", "/vagrant", disabled: true

    #open port 3500 for ssh redirection through bastion host

    dhcp_server.vm.network "forwarded_port", guest: 3500, host: 3500

    #open port 3501 for http redirection through gateway

    dhcp_server.vm.network "forwarded_port", guest: 3501, host: 3501

    dhcp_server.vm.hostname = "dhcpserver"

    dhcp_server.vm.network "private_network", ip: "192.168.42.0",
      virtualbox__intnet: "administration"
    
    dhcp_server.vm.network "private_network", ip: "192.168.42.64",
      virtualbox__intnet: "server"

    dhcp_server.vm.network "private_network", ip: "192.168.42.128",
      virtualbox__intnet: "employee"


    dhcp_server.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "dhcp_server"
      vb.memory = 2048
      vb.cpus = 2


    end


    # To setup python39
    dhcp_server.vm.provision "shell", inline: <<-SHELL
      pkg_add -r python-3.9.18
    SHELL

    dhcp_server.vm.provision "ansible" do |ansible|

      ansible.playbook = "dhcp_setup.yml"  

    end


  end



  config.vm.define "webserver" do |webserver|

    webserver.vm.box = "freebsd/FreeBSD-13.0-STABLE"

    webserver.vm.synced_folder ".", "/vagrant", disabled: true

    webserver.vm.hostname = "webserver"

    #webserver.vm.network "forwarded_port", guest: 22, host: 2200, disabled: true

    webserver.vm.network "private_network", type: "dhcp", virtualbox__intnet: "server"

    webserver.ssh.host = "localhost"

    webserver.ssh.port = 3500

    webserver.vm.provider "virtualbox" do |vb|

      vb.customize ["modifyvm", :id, "--nic1", "none"]
      vb.customize ["modifyvm", :id, "--macaddress2", "080027123456"]
      vb.gui = false
      vb.name = "webserver"
      vb.memory = 2048
      vb.cpus = 2

    end

    # To setup python39
    webserver.vm.provision "shell", inline: <<-SHELL
      pkg install -y python39
    SHELL
    
    webserver.vm.provision "ansible" do |ansible|

      ansible.playbook = "webserver_setup.yml"

    end

  end

  # config.vm.define "employee" do |employee|
    
  #   employee.vm.box = "debian/buster64"

  #   employee.vm.synced_folder ".", "/vagrant", disabled: true

  #   employee.vm.hostname = "employee"

  #   employee.vm.network "forwarded_port", guest: 22, host: 2200, disabled: true

  #   employee.vm.network "private_network", virtualbox__intnet: "employee"

  #   employee.vm.provider "virtualbox" do |vb|

  #     #vb.customize ["modifyvm", :id, "--nic1", "none"]
  #     vb.gui = true
  #     vb.name = "employee"
  #     vb.memory = 4096
  #     vb.cpus = 4

  #   end

    # config.vm.define "administration" do |administration|
    
    #   administration.vm.box = "debian/buster64"
  
    #   administration.vm.synced_folder ".", "/vagrant", disabled: true
  
    #   administration.vm.hostname = "administration"
  
    #   administration.vm.network "forwarded_port", guest: 22, host: 2200, disabled: true
  
    #   administration.vm.network "private_network", virtualbox__intnet: "administration"
  
    #   administration.vm.provider "virtualbox" do |vb|
  
    #     #vb.customize ["modifyvm", :id, "--nic1", "none"]
    #     vb.gui = true
    #     vb.name = "administration"
    #     vb.memory = 4096
    #     vb.cpus = 4
  
    #   end

end
