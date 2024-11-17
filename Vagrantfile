Vagrant.configure("2") do |config|

  # Máquina: Debian texto
  config.vm.define "nginx" do |nginx|
    nginx.vm.box = "debian/bookworm64"
    nginx.vm.hostname = "nginx.sistema.test"
    nginx.vm.network "private_network", ip: "192.168.57.102"

    # Sincronizar el directorio ./etc con /etc en la VM
    nginx.vm.synced_folder "./a", "/etc/vagrant", type: "virtualbox"
  end
end