# -*- mode: ruby -*-
# # vi: set ft=ruby :

$nodes = ENV['NUM_K8S_NODES'].to_i != 0 ? ENV['NUM_K8S_NODES'].to_i : 3

Vagrant.configure(2) do |config|

  (1..$nodes).each do |i|
    config.vm.define "k8s#{i}" do |s|
      s.ssh.forward_agent = true
      s.vm.box = "ubuntu/xenial64"
      s.vm.hostname = "k8s#{i}"
      s.vm.provision :shell, path: "scripts/bootstrap_ansible.sh"
      if i == 1
        s.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /vagrant/ansible/k8s-master.yml -c local"
      else
        s.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /vagrant/ansible/k8s-worker.yml -c local"
      end
      s.vm.network :private_network, :ip => "172.42.42.10#{i}", :netmask => "255.255.255.0", :auto_config => true
      s.vm.provider "virtualbox" do |v|
        v.name = "k8s#{i}"
        v.memory = 1024
        v.gui = false
      end
    end
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

end
