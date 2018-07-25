# -*- mode: ruby -*-
# vi: set ft=ruby :
require 'yaml'
hosts = YAML.load_file("hosts.yml")
Vagrant.configure(2) do |config|
	hosts.each do |host|
    	config.vm.define host['name'] do |host_vm|
			set_box(host, host_vm)
			set_cpus_and_memory(host, host_vm)
			set_private_ip(host, host_vm)
			update_host_with_package_manager(host, host_vm)
			install_packages(host, host_vm)
			run_install_scripts(host, host_vm)
		end
	end
end

def set_cpus_and_memory(host, host_vm)
	host_vm.vm.provider "virtualbox" do |vb|
		vb.cpus = host['cpus']
		vb.memory = host['memory'] 
	end
end

def set_box(host, host_vm)
	host_vm.vm.box = host['box']
end

def set_private_ip(host, host_vm)
	host_vm.vm.network "private_network", ip: host['private_ip']
end

def update_host_with_package_manager(host, host_vm)
	unless host['package_manager'].nil?
		host_vm.vm.provision "shell", inline: "sudo #{host['package_manager']} update -y"
		if host['package_manager'] == "apt"
			host_vm.vm.provision "shell", inline: "sudo #{host['package_manager']} upgrade -y"
		end
	end
end

def install_packages(host, host_vm)
	unless host['packages'].nil?
		if host['package_manager'] == "apk"
			host_vm.vm.provision "shell", inline: <<-SHELL
				sudo #{host['package_manager']} add #{host['packages'].join(" ")}
			SHELL
		else
			host_vm.vm.provision "shell", inline: <<-SHELL
				sudo #{host['package_manager']} install -y #{host['packages'].join(" ")}
			SHELL
		end
	end
end

def run_install_scripts(host, host_vm)
	unless host['scripts'].nil?
		host['scripts'].each do |script|
  			host_vm.vm.provision "shell", privileged: false, path: "vagrant_scripts/#{script}"
		end
	end
end

