Vagrant.configure("2") do |config|
    # List of servers
    servers=[
      {
        :hostname => "server2",
        :box => "ubuntu/focal64",
        :ip => "192.168.50.2",
        :ssh_port => '2212',
        :nginx_port => '81',
        :nginx_https_port => '444',
        :laravel_port => '8001'
      }
    ]
  
    servers.each do |machine|
      
      config.vm.define machine[:hostname] do |node|
        node.vm.box = machine[:box]
        node.vm.hostname = machine[:hostname]
      
        node.vm.network :private_network, ip: machine[:ip]
        node.vm.network "forwarded_port", guest: 22, host: machine[:ssh_port], id: "ssh"
        node.vm.network "forwarded_port", guest: 80, host: machine[:nginx_port], id: "http"
        node.vm.network "forwarded_port", guest: 443, host: machine[:nginx_https_port], id: "https"
        node.vm.network "forwarded_port", guest: machine[:laravel_port], host: machine[:laravel_port] # Проброс порта для приложения Laravel
        node.vm.provider :virtualbox do |v|
          v.customize ["modifyvm", :id, "--memory", 16384] # Установка оперативной памяти на 4 ГБ
          v.customize ["modifyvm", :id, "--cpus", 8]      # Установка количества процессоров на 2
          v.customize ["modifyvm", :id, "--name", machine[:hostname]]
        end
      end
  
    end
  
    # Inject your public SSH key
    id_rsa_key_pub = File.read(File.join(Dir.home, ".ssh", "id_rsa.pub"))
  
    config.vm.provision :shell,
          :inline => "echo 'appending SSH public key to ~vagrant/.ssh/authorized_keys' && echo '#{id_rsa_key_pub }' >> /home/vagrant/.ssh/authorized_keys && chmod 600 /home/vagrant/.ssh/authorized_keys"
  
    config.ssh.insert_key = false
  
    config.vm.provision "ansible" do |ansible|
      ansible.playbook = "site.yml"
    end
  end
  