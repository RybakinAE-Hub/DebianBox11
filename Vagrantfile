# -*- mode: ruby -*-
# vi: set ft=ruby :

# Прописываем общие параметры

INTERNAL_NET="192.168.82." # Префикс для LAN сети
DOMAIN="Rybakin.com"# Домен который будем использовать для всей площадки

servers=[# Массив из хешей, в котором заданы настройки для каждой виртуальной машины
  #{ :hostname => "net1." + DOMAIN, :ip_int => BRIDGE_NET + "101", :ram => 512, :cpus => "1" }, 
  #{ :hostname => "net2." + DOMAIN, :ip_int => BRIDGE_NET + "102", :ram => 512, :cpus => "1" },
  #{ :hostname => "net3." + DOMAIN, :ip_int => BRIDGE_NET + "103", :ram => 512, :cpus => "1" }, 
  { :hostname => "net4." + DOMAIN, :ip => INTERNAL_NET + "104", :ram => 2048, :cpus => 2 }
]
Vagrant.configure(2) do |config| # Входим в Главную конфигурацию vagrant версии 2
  #config.vm.synced_folder "V://", "/src/shara" # Добавить шару между хостовой и гостевой машиной
  config.vm.synced_folder ".", "/vagrant", disabled: true # Отключить дефолтную шару
  servers.each do |machine| # Проходим по элементам массива "servers"
    config.vm.define machine[:hostname] do |node|# Применяем конфигурации для каждой машины. Имя машины(как ее будет видно в Vbox GUI) находится в переменной "machine[:hostname]"
      node.vm.box = "Debian11"# Поднять машину из образа "ubuntu 14.04"
      node.vm.box_url = 'file:///V:/Debian11.box'# Также, можно указать URL откуда стянуть нужный box если такой есть (не обязательно)
      node.vm.usable_port_range = (2200..2250) # Пул портов, который будет использоваться для подключения к каждый VM через 127.0.0.1
      node.vm.hostname = machine[:hostname] # Hostname который будет присвоен VM (самой ОС)
      #node.vm.network "public_network"#, ip: machine[:ip], bridge: 'Intel(R) Ethernet Connection (2) i219 LM' # Добавление и настройка Bridge сетевого адаптера(моста). Чтобы узнать точное название birdge адаптера, нужно использовать VBoxManage.exe (смотрите ниже)
      node.vm.network "private_network", ip: machine[:ip]#, virtualbox__intnet: "VirtualBox Host-Only Ethernet Adapter #2" # Добавление и настройка внутреннего сетевого адаптера (Intnet)
      node.vm.communicator = "ssh" # через что будет подключение "ssh"-для линукса, "winrm"-для WINDOWS
      #node.ssh.private_key_path = "private_key" # Для доступа по ранее добавленному ключу
      #node.ssh.host = machine[:ip] # Домен/IP для подключения
      node.ssh.username = "vagrant" # SSH логин пользователя
      node.ssh.password = "vagrant" # SSH пароль
      node.vm.provider "virtualbox" do |vb|# Тонкие технические настройки для конкретного провайдера (в нашем случаи - VBoxManage) https://www.virtualbox.org/manual/ch08.html#vboxmanage-intro
        vb.customize ["modifyvm", :id, "--memory", machine[:ram], "--cpus", machine[:cpus]]# Размер RAM памяти и процесоров
        vb.name = machine[:hostname]# Можно перезаписать название VM в Vbox GUI
      end
      #node.vm.provision "shell", inline: <<-SCRIPT 
      #sudo apt-get update
      #sudo apt install sshpass ansible -y
      #SCRIPT
    end
  end
end