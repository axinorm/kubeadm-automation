# Variables
# Image à utiliser
IMAGE_NAME = "bento/ubuntu-20.04"
# RAM
MEM = 2048
# Nombre de CPU
CPU = 2
# Nombre de noeuds
WORKER_NB = 1
# Prefix pour la plage d'adresses IP à utiliser
NODE_NETWORK_BASE = "10.0.0"
# Master IP
MASTER_IP = "#{NODE_NETWORK_BASE}.10"

Vagrant.configure("2") do |config|
  config.ssh.insert_key = true

  # Configuration de la RAM et du CPU
  config.vm.provider "virtualbox" do |v|
    v.gui = false
    v.memory = MEM
    v.cpus = CPU
  end

  # Configuration du Master
  config.vm.define "k8s-master" do |master|
    master.vm.box = IMAGE_NAME
    master.vm.network "private_network", ip: MASTER_IP
    master.vm.hostname = "k8s-master"
  end

  # Configuration du/des Worker(s)
  (1..WORKER_NB).each do |i|
    config.vm.define "k8s-worker#{i}" do |worker|
      worker.vm.box = IMAGE_NAME
      worker.vm.network "private_network", ip: "#{NODE_NETWORK_BASE}.#{i + 10}"
      worker.vm.hostname = "k8s-worker#{i}"
    end
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "site.yml"
    ansible.groups = {  
      "master" => ["k8s-master"],
      "worker" => ["k8s-worker[1:#{WORKER_NB}]"],
      "kube_cluster:children" => ["master", "worker"],
      "kube_cluster:vars" => { "master_ip" => MASTER_IP }
    }
  end
end