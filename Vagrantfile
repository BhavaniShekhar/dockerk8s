# This is ruby script which will be creating Vagrant CentOS boxes
# Initiated by Ram
# Updated and reused by BhavaniShekhar
# Check your system configuration accordingly you can allocate resources in line 10, 11

Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Hello"
  
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "4096"
    vb.cpus = 2
   end

  config.vm.define "dockerhost" do |dockerhost|
    dockerhost.vm.box = "centos/7"
    dockerhost.vm.network "private_network", ip: "199.200.200.200"
    dockerhost.vm.hostname = "dockerhost"
  end
  
  config.vm.define "dockerclient1" do |dockerclient1|
    dockerclient1.vm.box = "centos/7"
    dockerclient1.vm.network "private_network", ip: "199.200.200.201"
    dockerclient1.vm.hostname = "dockerclient1"
  end


end
