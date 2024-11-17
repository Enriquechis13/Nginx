# Nginx

## 1 Instalación del Servidor Web Nginx en Debian

Este documento describe los pasos para instalar y verificar el servidor web **Nginx** en un sistema Debian.

### Requisitos previos

- Sistema operativo Debian (recomendado: **Debian Bookworm 64-bit**).
- Acceso a un usuario con privilegios `sudo`.

### Pasos para la instalación

**Actualizar los repositorios**  
   Ejecuta el siguiente comando para actualizar los repositorios locales:

```bash
   sudo apt update
```

**Instalar Nginx**
    Instala el paquete de Nginx utilizando el siguiente comando:

```bash
   sudo apt install nginx
```

**Verificar la instalación**
    Comprueba que el servicio de Nginx se haya instalado correctamente y que esté en funcionamiento:

```bash
   systemctl status nginx
```

   ![Imagen nginx](img/Captura.PNG)

## 2 Creación de la Carpeta del Sitio Web para Nginx

Este documento describe los pasos necesarios para configurar un directorio para el sitio web y verificar que el servidor está sirviendo las páginas correctamente.

### Requisitos previos

- Servidor web **Nginx** instalado y en ejecución.
- Acceso a un usuario con privilegios `sudo`.
- **Git** instalado en el servidor.

### Pasos para la configuración

**Crear la carpeta del sitio web**  
   Crea la estructura de directorios para tu sitio web :

```bash
   sudo mkdir -p /var/www/servidor_nginx/html
```

**Clonar el repositorio del sitio web**
   Clona el siguiente repositorio en la carpeta creada:

```bash
   git clone https://github.com/cloudacademy/static-website-example /var/www/servidor_nginx/html
```

**Configurar el propietario de la carpeta**
   Cambia el propietario de la carpeta y de su contenido al usuario www-data:

```bash
   sudo chown -R www-data:www-data /var/www/servidor_nginx/html
```

**Establecer permisos adecuados**
   Asigna permisos para evitar errores de acceso no autorizado:

```bash
   sudo chmod -R 755 /var/www/servidor_nginx
```

**Comprobar el sitio web**
   Abre un navegador y accede a:

```bash
   http://192.168.57.102
```

## 3. Configuración de Servidor Web NGINX

En Nginx existen dos directorios clave para la configuración de sitios web:

- **`/etc/nginx/sites-available/`**: Contiene los archivos de configuración de los sitios virtuales disponibles, es decir, todos los sitios web que podrían ser servidos por Nginx.

- **`/etc/nginx/sites-enabled/`**: Contiene los archivos de configuración de los sitios habilitados. Solo los sitios que están en este directorio se sirven efectivamente por Nginx.

El archivo de configuración por defecto (`default`) se encuentra en **`/etc/nginx/sites-available/`** y es el que se muestra cuando no se encuentra un sitio web o cuando se accede al servidor sin especificar un dominio.

### 2. Configuración de NGINX

#### 2.1. Crear un archivo de configuración para tu dominio

1. Accede a la máquina donde está instalado Nginx (tu servidor Debian).
   
2. Crea un archivo de configuración para tu nuevo dominio en el directorio **`/etc/nginx/sites-available/`**:

```bash
   sudo nano /etc/nginx/sites-available/servidor_nginx
```

Añade el siguiente contenido al archivo. Asegúrate de reemplazar **`/var/www/servidor_nginx/html`** por la ruta donde se encuentra el archivo index.html de tu sitio web y mi_nuevo_dominio por el nombre de tu dominio:

```bash
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
```

### 2.2. Crear un enlace simbólico en sites-enabled
Para habilitar el sitio, necesitas crear un enlace simbólico en el directorio **`/etc/nginx/sites-enabled/`**:

```bash
   sudo ln -s /etc/nginx/sites-available/servidor_nginx /etc/nginx/sites-enabled/
```
### 2.3. Reiniciar Nginx
Después de realizar estos cambios, reinicia el servicio de Nginx para que la nueva configuración tenga efecto:

```bash
   sudo systemctl restart nginx
```

## 4. Configuración de Servidor FTPS en Debian

### 2. Instalar y Configurar el Servidor FTPS

#### 2.1. Instalar VSFTPD

1. **Actualizar el sistema y los repositorios**:
   
   Asegúrate de que tu sistema esté actualizado antes de instalar cualquier paquete.

```bash
   sudo apt-get update
```

2. **Instalar VSFTPD**:

Instala `vsftpd`, un servidor FTP muy popular y seguro para sistemas Linux.

```bash
   sudo apt-get install vsftpd
```

#### 2.2. Crear un directorio para FTP

Crea un directorio en el que se almacenarán los archivos accesibles por FTP.

```bash
   mkdir /home/vagrant/ftp
```

#### 2.3. Crear certificados SSL

Para habilitar FTPS, necesitas crear certificados SSL que se utilizarán para cifrar las conexiones. 

1. **Genera los certificados**:

   Ejecuta el siguiente comando para generar un certificado SSL autofirmado para FTPS. Este proceso creará dos archivos: uno de clave privada y otro de certificado.

```bash
   sudo openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/ssl/private/vsftpd.key -out /etc/ssl/certs/vsftpd.crt
```

#### 2.4. Configuración de VSFTPD

1. **Editar el archivo de configuración de VSFTPD**:

Edita el archivo de configuración de `vsftpd` con el editor de texto que prefieras. Usamos `nano` en este caso.

```bash
   sudo nano /etc/vsftpd.conf
```

2. **Eliminar las líneas predeterminadas relacionadas con SSL**:

Dentro del archivo de configuración, busca y elimina las siguientes líneas:

```bash
   rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem 
   rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key 
   ssl_enable=NO
```

3. **Agregar las nuevas configuraciones de SSL**:

Reemplaza las líneas eliminadas con las siguientes configuraciones, ajustando las rutas y valores según corresponda:

```bash
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
```

Estas configuraciones activan el cifrado SSL, obligan a que los usuarios locales se conecten de forma segura, y definen la ruta local de los archivos accesibles por FTP.

#### 2.5. Reiniciar el servicio de VSFTPD

Una vez que hayas guardado los cambios en el archivo de configuración, reinicia el servicio `vsftpd` para aplicar la nueva configuración.

```bash
   sudo systemctl restart vsftpd
```

### 3. Acceder al Servidor FTPS

Ahora que el servidor FTPS está configurado, puedes acceder a él desde un cliente FTP, como **FileZilla**, utilizando las siguientes opciones de conexión:

#### 3.1. Conexión FTP sin cifrar (puerto 21)

Puedes conectarte a través de FTP estándar, pero **no es recomendable** debido a la falta de cifrado.

#### 3.2. Conexión FTPS (FTP seguro)

1. **FTP implícito sobre SSL**:  
   En esta modalidad, la conexión cifrada se establece de forma automática. Aunque esta variante era común en el pasado, **está desaconsejada hoy en día**.

2. **FTP explícito sobre SSL (FTPES)**:  
   Esta es la opción recomendada. En este caso, la conexión comienza en el puerto 21, pero luego se solicita explícitamente que la conexión se establezca de forma segura mediante SSL/TLS.

