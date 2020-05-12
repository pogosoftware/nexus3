# -*- mode: ruby -*-
# vi: set ft=ruby :

vmName = "Nexus 3"
vmCpus = 2
vmMemory = 2048

dataDisk = "E:\\Virtual Machines Disks\\nexus3\\nexus-data-disk.vdi"
dataDiskSize = 30720

Vagrant.configure("2") do |config|

  config.vm.define "nexus" do |nexus|
    nexus.vm.box = "bento/ubuntu-20.04"
    nexus.vm.network "private_network", ip: "192.168.192.100"

    nexus.vm.provider "virtualbox" do |vb|
      vb.gui = false
      vb.name = "#{vmName}"
      vb.cpus = "#{vmCpus}"
      vb.memory = "#{vmMemory}"
  
      unless File.exist?("#{dataDisk}")
        vb.customize ['createhd', '--filename', "#{dataDisk}", '--variant', 'Fixed', '--size', "#{dataDiskSize}"]
      end
      vb.customize ['storageattach', :id, '--storagectl', 'SATA Controller', '--port', 2, '--device', 0, '--type', 'hdd', '--hotpluggable', 'on', '--medium', "#{dataDisk}"]
    end

    nexus.vm.provision "shell", inline: <<-SHELL
      echo "Update nexus vm"
      apt-get update
      apt-get upgrade -y    

      echo "Instaling ansible, openjdk-8-jre-headless, python3-pip, apache2, jmespath"
      apt-get install -y ansible openjdk-8-jre-headless python3-pip apache2
      pip3 install jmespath

      echo "Enabling apache ssl, rewrite, proxy, proxy_http and headers mods"
      a2enmod ssl rewrite proxy proxy_http headers
      systemctl restart apache2
    SHELL

    nexus.vm.provision "ansible_local" do |ansible|
      ansible.install = false
      ansible.become = true
      ansible.playbook = "ansible/playbook.yml"
      ansible.galaxy_role_file = "ansible/requirements.yml"
      ansible.galaxy_roles_path = "/etc/ansible/roles"
      ansible.galaxy_command = "sudo ansible-galaxy install --role-file=%{role_file} --roles-path=%{roles_path} --force"
    end

    nexus.trigger.before :destroy do |trigger|
      trigger.warn = "Dettach Data Drive"
      trigger.ruby do |env, machine|
        system "VBoxManage", "storageattach", "#{machine.id}", "--storagectl", "SATA Controller", "--port", "2", "--device", "0", "--type", "hdd", "--medium", "none"
      end
    end
  end
end