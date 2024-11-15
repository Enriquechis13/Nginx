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
       ```bash
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

