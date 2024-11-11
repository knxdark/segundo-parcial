Vagrant.configure("2") do |config|
  
    # Configuración para el primer servidor Apache
    config.vm.define "web1" do |web1|
        web1.vm.box = "ubuntu/jammy64"
        web1.vm.hostname = "web1"
        web1.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web1.vm.network "private_network", ip: "192.168.33.10"
      web1.vm.synced_folder "./web1", "/var/www/html", owner: "www-data", group: "www-data", mount_options: ["dmode=775", "fmode=664"]

      web1.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
      
    end
  
    # Configuración para el segundo servidor Apache
    config.vm.define "web2" do |web2|
      web2.vm.box = "ubuntu/jammy64"
      web2.vm.hostname = "web2"
      web2.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      web2.vm.network "private_network", ip: "192.168.33.11"
      web2.vm.synced_folder "./web2", "/var/www/html", owner: "www-data", group: "www-data", mount_options: ["dmode=775", "fmode=664"]

      web2.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y apache2
        sudo systemctl enable apache2
        sudo systemctl start apache2
      SHELL
    
    end
  
    # Configuración para el servidor Nginx (Balanceador de carga)
    config.vm.define "load_balancer" do |load_balancer|
     load_balancer.vm.box = "ubuntu/jammy64"
     load_balancer.vm.hostname = "load_balancer"
     load_balancer.vm.provider "virtualbox" do |vb|
        vb.memory = "512"
        vb.cpus = 1
      end
      load_balancer.vm.network "private_network", ip: "192.168.33.12"

      load_balancer.vm.provision "shell", inline: <<-SHELL
        sudo apt-get update
        sudo apt-get install -y nginx
        sudo systemctl enable nginx
        sudo systemctl enable nginx






        cat <<EOF | sudo tee /etc/nginx/conf.d/load_balancer.conf
        upstream backend {
            server 192.168.33.10;
            server 192.168.33.11;
        }
  
        server {
            listen 80;

            location / {
                proxy_pass http://backend;
                proxy_set_header Host \$host;
                proxy_set_header x-Real-IP \$remote_addr;
                proxy_set_header X-Forwarded-For \$proxy_add_x_forwarded_for;
                proxy_set_header X-Forwarded-Proto \$scheme;
            }
        }
        EOF

        sudo  nginx -s reload
      SHELL
    end
  end
  