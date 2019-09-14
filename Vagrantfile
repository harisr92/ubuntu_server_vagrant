# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure('2') do |config|
  config.vm.provider 'virtualbox' do |vb|
    # Display the VirtualBox GUI when booting the machine
    vb.gui = false

    # Customize the amount of memory on the VM:
    vb.memory = '2048'
  end

  config.vm.define 'web' do |web|
    web.vm.box = 'generic/ubuntu1804'
    web.vm.network 'private_network', ip: '192.168.56.12'

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
    web.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'provisioning/playbook.yml'
      ansible.become = true
      ansible.compatibility_mode = '2.0'
    end
  end

  config.vm.define 'db' do |db|
    db.vm.box = 'generic/ubuntu1804'
    config.vm.network 'private_network', ip: '192.168.56.13'

    db.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'provisioning/playbook.yml'
      ansible.become = true
      ansible.compatibility_mode = '2.0'
    end
  end
end