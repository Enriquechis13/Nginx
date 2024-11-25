Vagrant.configure("2") do |config|

  # Máquina: Debian
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "debian/bookworm64"
    nginx.vm.hostname = "nginx.sistema.test"
    nginx.vm.network "private_network", ip: "192.168.57.102"

    nginx.vm.provision "shell", inline: <<-SHELL
      sudo apt-get update -y
      sudo apt-get upgrade -y

      sudo apt install -y nginx

      systemctl status nginx

      sudo mkdir -p /var/www/servidor_nginx/html

      sudo apt install git

      git clone https://github.com/cloudacademy/static-website-example /var/www/servidor_nginx/html

      sudo chown -R www-data:www-data /var/www/servidor_nginx/html

      sudo chmod -R 755 /var/www/servidor_nginx

      sudo bash -c 'cat > /etc/nginx/sites-available/servidor_nginx <<EOF
server {
    listen 80;
    listen [::]:80;

    root /var/www/servidor_nginx/html;
    index index.html index.htm index.nginx-debian.html;

    server_name servidor_nginx;

    location / {
        try_files $uri $uri/ =404;
    }
}
EOF'

      sudo ln -s /etc/nginx/sites-available/servidor_nginx/etc/nginx/sites-enabled/

      sudo systemctl restart nginx

      sudo apt-get install -y vsftpd

      sudo mkdir /home/vagrant/ftp

      sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt

      sudo cp /vagrant/vsftpd.conf /archivos_nginx/vsftpd.conf

      sudo systemctl restart vsftpd

      sudo apt install ufw
      
      sudo ufw allow 'Nginx Full'
      sudo ufw allow 21/tcp
      sudo ufw allow 990/tcp
      sudo ufw enable
sudo bash -c 'cat > /etc/nginx/sites-available/nginx_sitio <<EOF
    server {
    listen 80;
    server_name nginx_sitio.local;

    root /var/www/nginx_sitio/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
}
    EOF'

    sudo ln -s /etc/nginx/sites-available/nginx_sitio /etc/nginx/sites-enabled/
    
    sudo rm /etc/nginx/sites-enabled/default
    
    sudo systemctl restart nginx

    sudo mkdir -p /var/www/nginx_sitio/html
    sudo chown -R www-data:www-data /var/www/nginx_sitio/html
    sudo chmod -R 755 /var/www/nginx_sitio
    sudo chmod -R 775 /var/www/nginx_sitio
    sudo nano /etc/nginx/sites-available/nginx_sitio
    sudo systemctl restart nginx
    sudo ln -s /etc/nginx/sites-available/nginx_sitio /etc/nginx/sites-enabled/
    SHELL
  end
end
