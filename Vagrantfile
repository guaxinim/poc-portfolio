# -*- mode: ruby -*-
# vi: set ft=ruby :

##### Global Config
Vagrant.configure(2) do |config|
  hostsfile = "config-files/hosts"
  vhostfile = "config-files/httpd-vhost-keycloak.conf"
  #config.vm.network "forwarded_port", guest: 80, host: 8080, auto_correct: true

  # config.vm.synced_folder ".", "/vagrant", type: "nfs", mount_options: ""
  # :linux__nfs_options => ['rw','no_subtree_check','all_squash','async']
  config.vm.synced_folder ".", "/vagrant", type: "nfs", mount_options: ['defaults','vers=3','tcp','nolock'] 
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
  ipHost1 = "192.168.50.10"
  ipHost2 = "192.168.50.11"
  ipHost3 = "192.168.50.12"
  ######

  config.vm.provider "virtualbox" do |v|
    v.memory = 3072
    v.cpus = 2
  end

  config.vm.define "host1" do |host1|
    # host1.vm.hostname = "centos-master"
    host1.vm.box = "centos/7"
    # network
    host1.vm.network "private_network", ip: ipHost1
    # installs
    host1.vm.provision :file, :source => hostsfile, :destination => "/home/vagrant/hosts"
    host1.vm.provision "shell", inline: "sudo mv /home/vagrant/hosts /etc/hosts"
    host1.vm.provision "shell", inline: "sudo sed -i '/cachedir/c\cachedir=/vagrant/tmp/yum/$basearch/$releasever' /etc/yum.conf"
    host1.vm.provision "shell", inline: "sudo sed -i '/keepcache/c\keepcache=1' /etc/yum.conf"
    host1.vm.provision "shell", inline: "sudo yum update -y"
    host1.vm.provision "shell", inline: "sudo yum install -y ntpdate wget unzip java-1.8.0-openjdk"
    host1.vm.provision "shell", inline: "sudo yum update -y"
    # system services
    host1.vm.provision "shell", inline: "sudo systemctl enable ntpdate"
    host1.vm.provision "shell", inline: "sudo systemctl start ntpdate"
    host1.vm.provision "shell", inline: "sudo setenforce 0"
    host1.vm.provision "shell", inline: "sudo systemctl disable firewalld"
    host1.vm.provision "shell", inline: "sudo systemctl stop firewalld"
    host1.vm.provision "shell", inline: "sudo timedatectl set-timezone America/Sao_Paulo"
    host1.vm.provision "shell", inline: "sudo timedatectl set-ntp yes"
    # bpm suite startup
    host1.vm.provision "shell", inline: "cd /vagrant/content/jboss-bpm-suite-6.4.0/bin"
    host1.vm.provision "shell", run: "always", inline: "nohup ./standalone.sh -b 0.0.0.0 -bmanagement 0.0.0.0 -bunsecure 0.0.0.0 -Dorg.kie.override.deploy.enabled=true -Djbpm.enable.multi.con=true & sleep 1"
  end

end
