# -*- mode: ruby -*-
# vi: set ft=ruby :

ENV['VAGRANT_NO_PARALLEL'] = 'yes'

Vagrant.configure(2) do |config|
  config.vm.provision "shell", path: "bootstrap.sh"
  # the vagrant user is used to scp the file but you cannot access /etc/ with it
  # To make it work you need to upload it to a temp location and then use a shell
  # provisioner to move it to target directory
  config.vm.provision "file", 
    source: "/Users/alex/Documents/Youtube/ECK_36/k8s_ubuntu/daemon.json",
    destination: "/tmp/daemon.json"
  config.vm.provision "shell",
    inline: "mv /tmp/daemon.json /etc/docker/daemon.json"
  config.vm.provision "shell",
    inline: "sudo systemctl restart docker"
  # Kubernetes Master Server
  config.vm.define "kmaster" do |kmaster|
    kmaster.vm.box = "bento/ubuntu-18.04"
    kmaster.vm.hostname = "kmaster.example.com"
    kmaster.vm.network "private_network", ip: "172.42.42.100"
    kmaster.vm.provider "virtualbox" do |v|
      v.name = "kmaster"
      v.memory = 2048
      v.cpus = 2
    end
    kmaster.vm.provision "shell", path: "bootstrap_kmaster.sh"
  end

  NodeCount = 2

  # Kubernetes Worker Nodes
  (1..NodeCount).each do |i|
    config.vm.define "kworker#{i}" do |workernode|
      workernode.vm.box = "bento/ubuntu-18.04"
      workernode.vm.hostname = "kworker#{i}.example.com"
      workernode.vm.network "private_network", ip: "172.42.42.10#{i}"
      workernode.vm.provider "virtualbox" do |v|
        v.name = "kworker#{i}"
        v.memory = 4096
        v.cpus = 2
      end
      workernode.vm.provision "shell", path: "bootstrap_kworker.sh"
    end
  end

end
