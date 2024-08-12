ENV['VAGRANT_SERVER_URL'] = 'https://vagrant.elab.pro'

nodes = [
  { :hostname=> 'node01', :ip=> '10.11.10.2', :memory=> 2048, :cpu=> 1, :boxname=> 'ubuntu/focal64', :port=> 2222 },
  { :hostname=> 'node02', :ip=> '10.11.10.3', :memory=> 2048, :cpu=> 1, :boxname=> 'ubuntu/focal64', :port=> 2223 },
  { :hostname=> 'manager', :ip=> '10.11.10.1', :memory=> 2048, :cpu=> 1, :boxname=> 'ubuntu/focal64', :port=> 2221 }
]

Vagrant.configure("2") do |config|
  nodes.each do |node|
    config.vm.define node[:hostname] do |nodeconfig|
      nodeconfig.vm.box = node[:boxname]
      nodeconfig.vm.hostname = node[:hostname]
      nodeconfig.vm.network "forwarded_port", id: "ssh", guest: 22, host: node[:port]

      if node[:hostname] == 'node01'
        nodeconfig.vm.network "forwarded_port", guest: 8081, host: 8081
        nodeconfig.vm.network "forwarded_port", guest: 8087, host: 8087
      end
      
      nodeconfig.vm.network "private_network", ip: node[:ip], virtualbox__intnet: true

      if node[:hostname] == 'manager'
        nodeconfig.vm.provision "file", source: "./docker-compose.yml", destination: "~/docker-compose.yml"
        nodeconfig.vm.provision "file", source: "./services.tar.gz", destination: "~/services.tar.gz"
      end

      nodeconfig.vm.provision "shell" do |s|
          ssh_pub_key = File.readlines("#{ENV['USERPROFILE']}/.ssh/id_rsa.pub").first.strip
          s.inline = <<-SHELL
          echo #{ssh_pub_key} >> /home/vagrant/.ssh/authorized_keys
          SHELL
      end

      nodeconfig.vm.provider "virtualbox" do |vb|
          vb.name = node[:hostname]
          vb.memory = node[:memory]
          vb.cpus = node[:cpu]
      end
    end
  end
end