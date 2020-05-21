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
    web.vm.network "forwarded_port", guest: 5440, host: 5440

    # Enable provisioning with a shell script. Additional provisioners such as
    # Puppet, Chef, Ansible, Salt, and Docker are also available. Please see the
    # documentation for more information about their specific syntax and use.
    web.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'provisioning/playbook.yml'
      ansible.become = true
      ansible.compatibility_mode = '2.0'
      ansible.vault_password_file = "#{ENV['HOME']}/.vault-password"
    end

    web.vm.provision 'docker', after: 'ansible' do |docker|
      script = <<-SCRIPT
        docker pull $IMAGE; \
        docker service update --image $IMAGE --with-registry-auth $SWARM_SERVICE; \
        docker image prune --all --force; \
        echo "Deployed to swarm $SWARM_SERVICE";
      SCRIPT
      docker.post_install_provision(
        'shell',
        inline: script
      )
    end

    web.trigger.after :up do |trigger|
      trigger.info = 'Restart services'
      trigger.run_remote = {
        inline: "redis-server /etc/redis.conf"
      }
    end
  end

  config.vm.define 'alpine' do |alpine|
    alpine.vm.box = 'generic/alpine38'
    alpine.vm.network 'private_network', ip: '192.168.56.13'

    alpine.vm.provision 'ansible' do |ansible|
      ansible.playbook = 'provisioning/playbook.yml'
      ansible.become = true
      ansible.compatibility_mode = '2.0'
      ansible.vault_password_file = "#{ENV['HOME']}/.vault-password"
    end

    alpine.trigger.before :provision do |trigger|
      trigger.info = 'install python for ansible'
      trigger.run_remote = {
        inline: "apk add python docker; service docker start"
      }
    end

    alpine.trigger.before :destroy do |trigger|
      trigger.info = 'Leave node'
      trigger.run_remote = {
        inline: "docker swarm leave"
      }
    end
  end
end
