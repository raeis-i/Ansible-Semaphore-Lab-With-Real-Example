require_relative 'vars'
Vagrant.configure("2") do |config|
  config.vm.provider "virtualbox" do |vb|
    vb.memory = 1024
    vb.cpus = 1
  end

  (1..NUM_NODES).each do |node_id|
    config.vm.define "node#{node_id}" do |node_vm|
      node_vm.vm.box = IMAGE_Ubuntu
      node_vm.vm.hostname = "node#{node_id}"
      node_vm.vm.network "private_network", ip: "#{NETWORK_RANGE}#{node_id}"
      node_vm.vm.provider "virtualbox" do |v|
        v.name = "Node#{node_id}"
      end
      #Run bootstrap.sh in all nodes in order to change ssh setting and edit hosts
      node_vm.vm.provision "shell", path: "./scripts/bootstrap.sh", args: [NUM_NODES, NETWORK_RANGE]

      #Copy files and install docker and bring up sempaphore in node 1 only install docker and docker-compose and run Semaphoreui
      if node_id == 1
        node_vm.vm.provision "file", source: "semaphoreui", destination: "/home/vagrant/semaphoreui"
        node_vm.vm.network "forwarded_port", guest: 3000, host: 3000
        node_vm.vm.provision "shell", path: "./scripts/install-docker.sh"    
      end  
    end
  end
end