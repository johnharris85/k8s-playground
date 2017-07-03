# -*- mode: ruby -*-

Vagrant.configure(2) do |config|

  (1..3).each do |i|
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
        v.memory = 2048
        v.gui = false
      end
    end
  end

  config.vm.define "nfs" do |n|
    n.ssh.forward_agent = true
    n.vm.box = "ubuntu/xenial64"
    n.vm.hostname = "nfs-server"
    n.vm.provision :shell, path: "scripts/bootstrap_ansible.sh"
    n.vm.provision :shell, inline: "PYTHONUNBUFFERED=1 ansible-playbook /vagrant/ansible/nfs-server.yml -c local"
    n.vm.network :private_network, :ip => "172.42.42.200", :netmask => "255.255.255.0", :auto_config => true
    n.vm.provider "virtualbox" do |vn|
      vn.name = "nfs-server"
      vn.memory = 512
      vn.gui = false
    end
  end

  if Vagrant.has_plugin?("vagrant-cachier")
    config.cache.scope = :box
  end

end
