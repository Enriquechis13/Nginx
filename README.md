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