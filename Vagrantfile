# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'
dir = File.dirname(File.expand_path(__FILE__))
vconfig = YAML::load_file("#{dir}/config.yml")

# Vagrantfile API/syntax version. Don't touch unless you know what you're doing!
VAGRANTFILE_API_VERSION = "2"

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = "hashicorp/precise64"
  config.ssh.forward_agent = true

  config.vm.define "photos" do |photos|
    photos.vm.hostname = vconfig['vagrant_hostname']

    photos.vm.network "private_network", ip: vconfig['vagrant_ip']
 
    photos.vm.provider "virtualbox" do |vb|
      vb.customize ["modifyvm", :id, "--memory", "1024"]
      vb.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    end

    photos.vm.provision "shell", path: "scripts/bootstrap.sh"

    photos.vm.provision "ansible" do |ansible|
      ansible.groups = {
        "mongo" => [ "photos" ],
        "photos_api" => [ "photos" ],
        "photos_process" => [ "photos" ],
        "photos_web" => [ "photos" ],
      }

      ansible.playbook = "ansible/site.yml"

      ansible.extra_vars = { ansible_ssh_user: "vagrant" }
      ansible.sudo = true;
      ansible.vault_password_file = "~/.vault_pass.photos-deploy.txt";
    end
  end
end
