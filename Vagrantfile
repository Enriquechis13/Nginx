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

      sudo bash -c 'cat > /etc/vsftpd.conf <<EOF

rsa_cert_file=/etc/ssl/certs/vsftpd.crt
rsa_private_key_file=/etc/ssl/private/vsftpd.key
ssl_enable=YES
allow_anon_ssl=NO
force_local_data_ssl=YES
force_local_logins_ssl=YES
ssl_tlsv1=YES
ssl_sslv2=NO
ssl_sslv3=NO
require_ssl_reuse=NO
ssl_ciphers=HIGH
local_root=/home/vagrant/ftp
EOF'

      sudo systemctl restart vsftpd

      sudo apt install ufw
      
      sudo ufw allow 'Nginx Full'
      sudo ufw allow 21/tcp
      sudo ufw allow 990/tcp
      sudo ufw enable

    SHELL
  end
end
