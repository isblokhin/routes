# -*- mode: ruby -*-
# vim: set ft=ruby :
# -*- mode: ruby -*-
# vim: set ft=ruby :

MACHINES = {
  :R1 => {
  :box_name => "centos/7",
  :net => [ {ip: '192.168.10.1', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "vlan10"}, 
            {ip: '192.168.20.1', adapter: 3, netmask: "255.255.255.248", virtualbox__intnet: "vlan20"}, ]
  },

  :R2 => {
  :box_name => "centos/7",
  :net => [  {ip: '192.168.10.2', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "vlan10"},
             {ip: '192.168.30.2', adapter: 3, netmask: "255.255.255.248", virtualbox__intnet: "vlan30"}, ]
  },
  :R3 => {
  :box_name => "centos/7",
  :net => [  {ip: '192.168.20.2', adapter: 2, netmask: "255.255.255.248", virtualbox__intnet: "vlan20"},
	     {ip: '192.168.30.1', adapter: 3, netmask: "255.255.255.248", virtualbox__intnet: "vlan30"}, ]
  } }

Vagrant.configure("2") do |config|

  MACHINES.each do |boxname, boxconfig|

    config.vm.define boxname do |box|
    box.vm.box = boxconfig[:box_name]
    box.vm.host_name = boxname.to_s
    boxconfig[:net].each do |ipconf|
    box.vm.network "private_network", ipconf
    end
    
    if boxconfig.key?(:public)
    box.vm.network "public_network", boxconfig[:public]
    end

    box.vm.provision "shell", inline: <<-SHELL
    mkdir -p ~root/.ssh
    cp ~vagrant/.ssh/auth* ~root/.ssh
    SHELL
        
    case boxname.to_s
    when "R1"
    config.vm.provision "ansible" do |ansible|
#    ansible.verbose = "vvv" 
    ansible.playbook = "playbook1.yml" 
    ansible.sudo = "true"
    end
    box.vm.provision "shell", run: "always", inline: <<-SHELL
     echo -e "192.168.10.1 192.168.10.1 R1" >> /etc/hosts
     echo -e "192.168.10.2 192.168.30.2 R2" >> /etc/hosts
     echo -e "192.168.20.2 192.168.30.1 R3" >> /etc/hosts
      SHELL
    when "R2"
    config.vm.provision "ansible" do |ansible|
#    ansible.verbose = "vvv"
    ansible.playbook = "playbook2.yml"
    ansible.sudo = "true"
    end
    box.vm.provision "shell", run: "always", inline: <<-SHELL
      sysctl net.ipv4.conf.all.forwarding=1	
     echo -e "192.168.10.1 192.168.10.1 R1" >> /etc/hosts
     echo -e "192.168.10.2 192.168.30.2 R2" >> /etc/hosts
     echo -e "192.168.20.2 192.168.30.1 R3" >> /etc/hosts
      SHELL
    when "R3"
    config.vm.provision "ansible" do |ansible|
#    ansible.verbose = "vvv"
    ansible.playbook = "playbook3.yml"
    ansible.sudo = "true"
    end
    box.vm.provision "shell", run: "always", inline: <<-SHELL
      sysctl net.ipv4.conf.all.forwarding=1
     echo -e "192.168.10.1 192.168.10.1 R1" >> /etc/hosts
     echo -e "192.168.10.2 192.168.30.2 R2" >> /etc/hosts
     echo -e "192.168.20.2 192.168.30.1 R3" >> /etc/hosts
       SHELL
     end
    end
   end
end

