# -*- mode: ruby -*-
# vi: set ft=ruby :

N_CTRLRS = 3
N_WRKRS = 3

Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/bionic64"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
  end

  config.vm.provision "ansible" do |ansible|
    ansible.playbook = "k8s_hard_way.yml"
    ansible.host_vars = {}

    (1..N_CTRLRS).each do |i|
      config.vm.define "controller-#{i}" do |controller|
        controller.vm.hostname = "controller-#{i}"
        controller.vm.network "private_network", ip: "10.0.0.1#{i}"
        ansible.host_vars["controller-#{i}"] = { "internal_ip" => "10.0.0.1#{i}" }
      end
    end

    (1..N_WRKRS).each do |i|
      config.vm.define "worker-#{i}" do |worker|
        worker.vm.hostname = "worker-#{i}"
        worker.vm.network "private_network", ip: "10.0.0.5#{i}"
        ansible.host_vars["worker-#{i}"] = { "internal_ip" => "10.0.0.5#{i}", "pod_cidr" => "10.200.#{i}.0/24" }
      end
    end

    config.vm.define "balancer" do |balancer|
      balancer.vm.hostname = "balancer"
      balancer.vm.network "private_network", ip: "10.0.0.100"
        ansible.host_vars["balancer"] = { "internal_ip" => "10.0.0.100", "aliases" => "kubernetes" }
    end

    #ansible.host_vars = {
    #  "controller-1" => { "internal_ip" => "10.0.0.11" },
    #  "controller-2" => { "internal_ip" => "10.0.0.12" },
    #  "controller-3" => { "internal_ip" => "10.0.0.13" },
    #  "worker-1" => { "internal_ip" => "10.0.0.51" },
    #  "worker-2" => { "internal_ip" => "10.0.0.52" },
    #  "worker-3" => { "internal_ip" => "10.0.0.53" },
    #  "balancer" => { "internal_ip" => "10.0.0.100" },
    #}
    ansible.groups = {
      "controllers" => ["controller-[1:#{N_CTRLRS}]"],
      "workers" => ["worker-[1:#{N_WRKRS}]"],
      "balancers" => ["balancer"],
    }
  end
end
