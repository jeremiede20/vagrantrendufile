# -*- mode: ruby -*-
# vi: set ft=ruby :

######### Le partage est le même sur les deux serveurs web. Il faut mofifier le fichier index.php en reseignant du texte à la ligne :
######### echo "<strong>Renseigner ici le texte et enregistrer</strong>";

######### Se connecter à l'interface web haproxy : http://127.0.0.1:8080/haproxy?stats/
######### Se connecter aux pages web : http://127.0.0.1:8081/

#Choix de la distribution => ubuntu/trusty64
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/trusty64"
  config.vm.box_url = "https://vagrantcloud.com/ubuntu/trusty64"
  
  #Configurer les VMs en choissant la taille de la mémoire et le nombre de processeur
  config.vm.provider "virtualbox" do |v|
    v.customize ["modifyvm", :id, "--memory", 1024, "--cpus", 1]
  end
  
  #Configurer la VM haproxy
  config.vm.define :haproxy, primary: true do |haproxy_config|
    #Nom de la VM
    haproxy_config.vm.hostname = 'haproxy'
	#Désactiver le partage par défaut
	haproxy_config.vm.synced_folder ".", "/vagrant", disabled: true
	#Configurer les ports de la page d'administration et de la page web
    haproxy_config.vm.network :forwarded_port, guest: 8080, host: 8080
    haproxy_config.vm.network :forwarded_port, guest: 80, host: 8081
	#Configuration IP
    haproxy_config.vm.network :private_network, ip: "192.168.33.10", netmask: "255.255.255.0"
	#Appeler le script sh pour la configuration logicielle après l'installation de la VM
    haproxy_config.vm.provision :shell, :path => "haproxy-setup.sh"
  end

  config.vm.define :web1, second: true do |web1_config|
    #Nom de la VM
    web1_config.vm.hostname = 'web1'
	#Désactiver le partage par défaut
	web1_config.vm.synced_folder ".", "/vagrant", disabled: true
	#Configurer le partage
	#Attention chemin à modifier selon emplacement
    web1_config.vm.synced_folder "/Documents/EPSI/B3/Virtualisation/Partage/data_web", "/var/www/html"
	#Configuration IP
    web1_config.vm.network :private_network, ip: "172.28.33.11", netmask: "255.255.255.0"
	#Appeler le script sh pour la configuration logicielle après l'installation de la VM
    web1_config.vm.provision :shell, :path => "web-setup.sh"
  end

  config.vm.define :web2, third: true do |web2_config|
    #Nom de la VM
    web2_config.vm.hostname = 'web2'
	#Désactiver le partage par défaut
	web2_config.vm.synced_folder ".", "/vagrant", disabled: true
	#Configurer le partage
	#Attention chemin à modifier selon emplacement
    web2_config.vm.synced_folder "/Documents/EPSI/B3/Virtualisation/Partage/data_web", "/var/www/html"
	#Configuration IP
    web2_config.vm.network :private_network, ip: "172.28.33.12", netmask: "255.255.255.0"
	#Appeler le script sh pour la configuration logicielle après l'installation de la VM
    web2_config.vm.provision :shell, :path => "web-setup.sh"
  end
end
