$install_docker = <<SCRIPT
echo Installing Docker...
curl -sSL https://get.docker.com/ | sh
sudo usermod -aG docker ubuntu
SCRIPT
$master_script = <<SCRIPT
echo Swarm Init...
sudo docker swarm init --listen-addr 10.0.0.180:2377 --advertise-addr 10.0.0.180:2377
sudo docker swarm join-token --quiet worker > /vagrant/worker_token
SCRIPT
$node_script = <<SCRIPT
echo Swarm Join...
sudo docker swarm join --token $(cat /vagrant/worker_token) 10.0.0.180:2377
SCRIPT

Vagrant.configure('2') do |config|
vm_box = 'ubuntu/focal64'
config.vm.define :master, primary: true  do |master|
    master.vm.box = vm_box
    master.vm.box_check_update = true
    master.vm.network :public_network, ip: "10.0.0.180"
    master.vm.hostname = "master"
    master.vm.synced_folder ".", "/vagrant"
    master.vm.provision "shell", inline: $install_docker, privileged: true
    master.vm.provision "shell", inline: $master_script, privileged: true
    master.vm.provider "virtualbox" do |vb|
      vb.name = "master"
      vb.memory = "1024"
    end
  end
(1..3).each do |i|
    config.vm.define "node0#{i}" do |node|
      node.vm.box = vm_box
      node.vm.box_check_update = true
      node.vm.network :public_network, ip: "10.0.0.18#{i}"
      node.vm.hostname = "node0#{i}"
      node.vm.synced_folder ".", "/vagrant"
      node.vm.provision "shell", inline: $install_docker, privileged: true
      node.vm.provision "shell", inline: $node_script, privileged: true
      node.vm.provider "virtualbox" do |vb|
        vb.name = "node0#{i}"
        vb.memory = "1024"
      end
    end
  end
end