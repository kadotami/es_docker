
Vagrant.configure("2") do |config|
  servers = [
    { "name" => "ci", "memory" => '512', "ip" => "192.168.33.11", "ssh" => 2001, "ci" => true },
    { "name" => "node1", "memory" => '2048', "ip" => "192.168.33.12", "ssh" => 2002, "ci" => false } 
  ]

  script = <<SCRIPT
  mkdir -p /home/vagrant/ansible-playbook
  sudo chmod 600 /home/vagrant/.ssh
  sudo chmod 700 /home/vagrant/.ssh/authorized_keys
  sudo cat /home/vagrant/ansible-playbook/vagrant/public_key >> /home/vagrant/.ssh/authorized_keys
  sudo yum -y install epel-release
  sudo yum -y install ansible
SCRIPT
    

  servers.each do |server|
    config.vm.define server["name"] do |node|
      node.vm.box = "bento/centos-7.4"
      node.vm.network :forwarded_port, guest: 22, host: server["ssh"], id: "ssh"
      node.vm.network :private_network, ip: server["ip"]
      
      node.vm.provider "virtualbox" do |v|
        v.customize ["modifyvm", :id, "--memory", server["memory"]]
      end

      if server["ci"]
        node.vm.synced_folder "./", "/home/vagrant/ansible-playbook"        
        node.vm.provision :shell, :inline => script
      end
      node.ssh.private_key_path = "./vagrant/inseccure_private_key"
      node.ssh.insert_key = false
    end
  end
end
