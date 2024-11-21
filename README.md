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

## 5. Instrucciones para Transferir Archivos del Sitio Web mediante FTPES

### Requisitos previos

**Servidor FTPS configurado**: Asegúrate de haber configurado correctamente el servidor FTPS en tu máquina virtual Debian, siguiendo los pasos que se detallan en la sección anterior.

**Acceso a un cliente FTP**: En este caso, utilizaremos FileZilla, que es un cliente FTP que soporta conexiones FTPS.
Directorio de destino en el servidor: Asegúrate de tener creado el directorio /var/www/mi_nuevo_sitio/html en tu servidor, donde subirás los archivos del sitio web.

1. **Configurar FileZilla para usar FTPES**

#### 1.1. Descargar e instalar FileZilla
Si no tienes FileZilla instalado, puedes descargarlo desde su página oficial https://filezilla-project.org/. Es compatible con Windows, Linux y macOS.

#### 1.2. Abrir FileZilla
Una vez instalado, abre FileZilla en tu máquina local.

#### 1.3. Configurar una nueva conexión FTPES

En FileZilla, ve al menú Archivo y selecciona Gestor de sitios.
En la ventana del gestor de sitios, haz clic en Nuevo sitio y asigna un nombre descriptivo para la conexión (por ejemplo, "Servidor FTPS").

**Completa los siguientes campos**:

**Host**: La dirección IP de tu servidor Debian (por ejemplo, 192.168.57.102).
**Puerto**: 21 (puerto predeterminado para FTPES).
**Protocolo**: Selecciona FTP - Protocolo de transferencia de archivos.
**Cifrado**: Selecciona Requiere FTP sobre TLS explícito (FTPES).
**Modo de acceso**: Elige Normal.
**Usuario**: El nombre de usuario de tu cuenta FTP en el servidor Debian.
**Contraseña**: La contraseña asociada a la cuenta FTP en tu servidor.

#### 1.4. Conectar al servidor
Haz clic en Conectar para iniciar la conexión con el servidor FTPS.

2. **Subir los Archivos al Directorio /var/www/nginx_sitio/html**

#### 2.1. Navegar a la carpeta local
En el panel izquierdo de FileZilla (la vista local), navega a la carpeta donde tienes los archivos de tu sitio web que deseas transferir.

#### 2.2. Navegar al directorio de destino en el servidor
En el panel derecho de FileZilla (la vista remota), navega hasta el directorio /var/www/mi_nuevo_sitio/html en tu servidor Debian.

#### 2.3. Subir los archivos
Selecciona todos los archivos y carpetas de tu sitio web en el panel izquierdo (local).
Arrástralos y suéltalos en el panel derecho (remoto), en el directorio /var/www/nginx_sitio/html.
FileZilla comenzará a transferir los archivos al servidor. Asegúrate de que todos los archivos se transfieran correctamente sin errores.

3. **Verificación de la Transferencia**

#### 3.1. Verificar en FileZilla
FileZilla mostrará el progreso de la transferencia de archivos en la parte inferior. Una vez completada la transferencia, asegúrate de que todos los archivos y carpetas estén presentes en el directorio /var/www/nginx_sitio/html.

#### 3.2. Verificar en el servidor
Puedes verificar que los archivos han sido subidos correctamente al servidor accediendo a tu máquina virtual Debian y ejecutando el siguiente comando para listar los archivos en el directorio:

```bash
   ls /var/www/nginx_sitio
```

4. **Conclusión**
Con estos pasos, has transferido exitosamente los archivos de tu sitio web a tu servidor Debian usando FTPES (FTP seguro) a través de FileZilla. Ahora tu sitio web está listo para ser accesible a través de Nginx u otro servidor web que hayas configurado en tu servidor.

Si encuentras algún problema durante la transferencia, asegúrate de revisar los registros de FileZilla y verificar la configuración del servidor FTPS en Debian.  

## 6. Cuestiones finales

### ¿Qué pasa si no hago el link simbólico entre `sites-available` y `sites-enabled` de mi sitio web?

Si no creas el link simbólico entre el archivo de configuración en **`/etc/nginx/sites-available/`** y **`/etc/nginx/sites-enabled/`**, Nginx no reconocerá el sitio web que has configurado, lo que significa que:

- **El sitio no estará habilitado**: Nginx no sabrá que el nuevo dominio o sitio web debe estar activo, por lo que no podrá servir las páginas de tu sitio web.
- **No habrá acceso a tu sitio web**: Cuando intentes acceder a tu sitio web desde un navegador, recibirás una página de error o la página predeterminada de Nginx, si está configurada, en lugar de tu sitio web.
  
En resumen, **el sitio web no será accesible** a menos que crees el link simbólico y reinicies Nginx.

### ¿Qué pasa si no le doy los permisos adecuados a `/var/www/nginx_sitio`?

Si no otorgas los permisos adecuados a la carpeta **`/var/www/nginx_sitio`**, pueden ocurrir los siguientes problemas:

- **Nginx no podrá acceder a los archivos**: Nginx necesita poder leer los archivos dentro del directorio de tu sitio web (por ejemplo, `index.html`, imágenes, etc.). Si los permisos son incorrectos, Nginx no podrá servir las páginas y mostrará un error.

- **Problemas de seguridad**: Si los permisos son demasiado permisivos (por ejemplo, si cualquier usuario tiene acceso de escritura), esto puede ser un riesgo de seguridad, ya que alguien podría modificar o eliminar archivos críticos de tu sitio.

- **Error 403 (Forbidden)**: Si los permisos de acceso son demasiado restrictivos (por ejemplo, si no se da permiso de lectura al grupo o a Nginx), podrías recibir un error 403 al intentar acceder a tu sitio web.

- **Imposibilidad de cargar archivos o subir contenido**: Si estás usando un sistema para subir archivos al servidor (por ejemplo, con FTP), es probable que no puedas cargar o modificar los archivos sin los permisos adecuados.

### Recomendaciones:

- **Permisos adecuados**: Asegúrate de que **el usuario y grupo de Nginx** (generalmente `www-data`) tenga permisos de lectura y, en su caso, de escritura en el directorio de tu sitio web.
- **Seguridad**: No utilices permisos demasiado amplios (como `777`) para evitar riesgos de seguridad. Usa permisos como `755` para directorios y `644` para archivos.

# Autenticación en Nginx

## 1. Configuración Inicial: Autenticación Básica con OpenSSL

### 1.1 Instalación de OpenSSL

Primero, asegúrate de que OpenSSL esté instalado en tu servidor. Para comprobar si está instalado, ejecuta el siguiente comando:

```bash
   dpkg -l | grep openssl
```

Si OpenSSL no está instalado, puedes instalarlo con los siguientes comandos:

```bash
   sudo apt update
   sudo apt install openssl
```

### 1.2 Creación de Usuarios y Contraseñas
A continuación, crearás el archivo .htpasswd que contendrá los usuarios y sus contraseñas cifradas.

**Crear el archivo .htpasswd:**

Para crear el archivo .htpasswd y agregar un usuario.

```bash
   sudo sh -c "echo -n 'enrique' >> /etc/nginx/.htpasswd"
```

**Cifrar la contraseña:**

Luego, utiliza OpenSSL para cifrar la contraseña. Se te pedirá que introduzcas una contraseña:

```bash
   sudo sh -c "openssl passwd -apr1 >> /etc/nginx/.htpasswd"
```

Para agregar más usuarios, repite el proceso con un nuevo nombre de usuario y contraseña.

**Verificar el contenido cifrado del archivo:**

Para asegurarte de que los usuarios y contraseñas se han agregado correctamente, puedes visualizar el contenido cifrado del archivo .htpasswd ejecutando:

```bash
   cat /etc/nginx/.htpasswd
```

## 2. Configuración del Servidor Nginx

### 2.1 Proteger el Acceso al Sitio Completo

**Editar la configuración de Nginx:**

Edita el archivo de configuración del sitio web en `/etc/nginx/sites-available/nginx_web`:

```bash
   sudo nano /etc/nginx/sites-available/nginx_web
```

Agregar la autenticación básica:

Dentro del bloque del servidor `(server { ... })`, agrega la configuración para proteger el acceso al sitio web con autenticación básica:

```bash
   server {
      listen 80;
      root /var/www/deaw/html/simple-static-website;
      index index.html index.htm index.nginx-debian.html;
      server_name nombre_web;

      location / {
         auth_basic "Área restringida";
         auth_basic_user_file /etc/nginx/.htpasswd;
         try_files $uri $uri/ =404;
      }
   }
```

Guarda los cambios y cierra el archivo.

Despues reinicia nginx:

```bash
   sudo systemctl restart nginx
```