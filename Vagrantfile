# -*- mode: ruby -*-
# vi: set ft=ruby :

##### Global Config
Vagrant.configure(2) do |config|
  hostsfile = "config-files/hosts"
  vhostfile = "config-files/httpd-vhost-keycloak.conf"
  #config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

  config.vm.synced_folder ".", "/vagrant", type: "nfs"
  if Vagrant.has_plugin? "vagrant-vbguest"
    config.vbguest.no_install  = true
    config.vbguest.auto_update = false
    config.vbguest.no_remote   = true
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.auto_detect = true
    config.cache.scope = :machine
    config.cache.enable :yum
    config.cache.synced_folder_opts = {
      type: :nfs,
      mount_options: ['rw', 'vers=3', 'tcp', 'nolock']
    }
  end


  ###### Keycloak cluster
  ###### IP Addresses
  ipMaster = "192.168.33.10"
  ipHost1 = "192.168.33.11"
  ipHost2 = "192.168.33.12"
  ipHost3 = "192.168.33.13"
  ######

  config.vm.define "kc0" do |kc0|
    #kc0.vm.hostname = "centos-master"
    kc0.vm.box = "centos/7"
    # network
    kc0.vm.network "private_network", ip: ipMaster
    # installs
    kc0.vm.provision :file, :source => hostsfile, :destination => "/home/vagrant/hosts"
    kc0.vm.provision "shell", inline: "sudo mv /home/vagrant/hosts /etc/hosts"
    kc0.vm.provision "shell", inline: "sudo sed -i '/cachedir/c\cachedir=/vagrant/tmp/yum/$basearch/$releasever' /etc/yum.conf"
    kc0.vm.provision "shell", inline: "sudo sed -i '/keepcache/c\keepcache=1' /etc/yum.conf"
    kc0.vm.provision "shell", inline: "sudo yum update -y"
    kc0.vm.provision "shell", inline: "sudo yum install -y ntpdate wget unzip java-1.8.0-openjdk"
    kc0.vm.provision "shell", inline: "sudo yum update -y"
    # system services
    kc0.vm.provision "shell", inline: "sudo systemctl enable ntpdate"
    kc0.vm.provision "shell", inline: "sudo systemctl start ntpdate"
    kc0.vm.provision "shell", inline: "sudo setenforce 0"
    kc0.vm.provision "shell", inline: "sudo systemctl disable firewalld"
    kc0.vm.provision "shell", inline: "sudo systemctl stop firewalld"
    kc0.vm.provision "shell", inline: "sudo timedatectl set-timezone America/Sao_Paulo"
    kc0.vm.provision "shell", inline: "sudo timedatectl set-ntp yes"
    # keycloak installation
    # kc0.vm.provision :file, :source => keycloakfile, :destination => "/home/vagrant/#{keycloakversion}.zip"
    # kc0.vm.provision "shell", inline: "unzip -o /home/vagrant/#{keycloakversion}.zip -d /home/vagrant;ln -s -f #{keycloakversion} keycloak"
    kc0.vm.provision "shell", inline: "sudo chown -R vagrant:vagrant /home/vagrant"
    # kc0.vm.provision "shell", inline: "/home/vagrant/keycloak/bin/add-user-keycloak.sh -u admin -p keycloak@123"
    # kc0.vm.provision :file, :source => keycloakfile, :destination => "/home/vagrant/keycloak/standalone/configuration/keycloak.jks"
    # kc0.vm.provision "shell", run: "always", inline: "nohup /home/vagrant/keycloak/bin/standalone.sh -b 0.0.0.0 -Djboss.server.data.dir=/vagrant/data & sleep 1"
  end

end
