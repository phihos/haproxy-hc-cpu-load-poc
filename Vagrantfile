# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  config.vm.box = "base"

  config.vm.define "haproxy-high-cpu" do |hph|
    hph.vm.box = "ubuntu/focal64"
    hph.vm.network "forwarded_port", guest: 8080, host: 8080
    
    hph.vm.provision "file", source: "haproxy-high-cpu.cfg", destination: "/tmp/haproxy.cfg"
    hph.vm.provision "shell", inline: <<-SHELL
      mv /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg
      chown haproxy:haproxy /etc/haproxy/haproxy.cfg
      systemctl restart haproxy
    SHELL
  end
 
  config.vm.define "haproxy-low-cpu" do |hpl|
    hpl.vm.box = "ubuntu/focal64"
    hpl.vm.network "forwarded_port", guest: 8080, host: 8081

    hpl.vm.provision "file", source: "haproxy-low-cpu.cfg", destination: "/tmp/haproxy.cfg"
    hpl.vm.provision "shell", inline: <<-SHELL
      mv /tmp/haproxy.cfg /etc/haproxy/haproxy.cfg
      chown haproxy:haproxy /etc/haproxy/haproxy.cfg
      systemctl restart haproxy
    SHELL
  end

  config.vm.provision "shell", inline: <<-SHELL
    apt-get update
    apt-get install -y --no-install-recommends software-properties-common
    add-apt-repository -y ppa:vbernat/haproxy-2.6
    apt-get install -y haproxy=2.6.\*
  SHELL
  
  config.vm.provision "shell", run: 'always', inline: <<-SHELL
     # add iptales rule to simulate a server being down
    iptables -A OUTPUT -p tcp --dst 8.8.8.8 --dport 8000 -j DROP
  SHELL
end
