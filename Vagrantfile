# -*- mode: ruby -*-
# vi: set ft=ruby :

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = '2'

# noinspection RubyResolve
Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  os = 'bento/ubuntu-16.04'
  net_ip = '192.168.50'

  # noinspection RubyResolve
  config.vm.define :master, primary: true do |master_config|
    # noinspection RubyResolve
    master_config.vm.provider 'virtualbox' do |vb|
      vb.memory = '2048'
      vb.cpus = 1
      vb.name = 'master'
    end
    master_config.vm.box = "#{os}"
    master_config.vm.host_name = 'saltmaster.local'
    master_config.vm.network 'private_network', ip: "#{net_ip}.10"
    master_config.vm.synced_folder 'saltstack/salt/', '/srv/salt'
    master_config.vm.synced_folder 'saltstack/pillar/', '/srv/pillar'

    # noinspection RubyResolve
    master_config.vm.provision :salt do |salt|
      salt.master_config = 'saltstack/etc/master'
      salt.master_key = 'saltstack/keys/master_minion.pem'
      salt.master_pub = 'saltstack/keys/master_minion.pub'
      salt.minion_key = 'saltstack/keys/master_minion.pem'
      salt.minion_pub = 'saltstack/keys/master_minion.pub'
      # noinspection RubyStringKeysInHashInspection
      salt.seed_master = {
          'minion1' => 'saltstack/keys/minion1.pub',
          'minion2' => 'saltstack/keys/minion2.pub'
      }

      salt.install_type = 'git'
      salt.install_master = true
      salt.no_minion = true
      salt.verbose = true
      salt.colorize = true
      salt.bootstrap_options = '-P -c /tmp'
    end
  end


  # noinspection RubyResolve,RubyScope
  [
      ['minion1', "#{net_ip}.11", '1024', os],
      ['minion2', "#{net_ip}.12", '1024', os],
  ].each do |vmname, ip, mem, os|
    # noinspection RubyResolve
    config.vm.define "#{vmname}" do |minion_config|
      # noinspection RubyResolve
      minion_config.vm.provider 'virtualbox' do |vb|
        vb.memory = "#{mem}"
        vb.cpus = 1
        vb.name = "#{vmname}"
      end
      minion_config.vm.box = "#{os}"
      minion_config.vm.hostname = "#{vmname}"
      minion_config.vm.network 'private_network', ip: "#{ip}"

      # noinspection RubyResolve
      minion_config.vm.provision :salt do |salt|
        salt.minion_config = "saltstack/etc/#{vmname}"
        salt.minion_key = "saltstack/keys/#{vmname}.pem"
        salt.minion_pub = "saltstack/keys/#{vmname}.pub"
        salt.install_type = 'git'
        salt.verbose = true
        salt.colorize = true
        salt.bootstrap_options = '-P -c /tmp'
      end
    end
  end
end
