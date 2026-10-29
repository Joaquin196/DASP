## Ejercicio A1.1: La lista de las siete carencias 

1. Se utiliza ROOT y no requiere contraseña

2. Hay que iniciar el panel de control de Xampp, abrir el servidor de Apache y mySQL y abrir la URL del 
proyecto (http:/localhost/proyectoMercado/index.php)

3. Queda registrado en la ruta C:\xampp\php\logs\php_error_log

4. Mi versión es la php 8.2.12 y la de mi compañero es la misma


## Ejercicio A1.2: Las dos máquinas del grupo

**Proceso de creación y comandos principales:**
1. Creamos la máquina base con Debian 13. Como el instalador de Debian deja `sudo` sin instalar si se pone contraseña de root, entramos como root e instalamos los paquetes esenciales:
   `apt update && apt install sudo openssh-server`
2. Convertimos la máquina en plantilla y la clonamos dos veces (`dev.grupo7` y `pro.grupo7`).
3. Asignamos las IPs estáticas editando el archivo de red y comprobamos conectividad.

**Errores y problemas documentados:**
* **Problema de red al clonar:** Para evitar que ambas máquinas colisionaran en la red local heredando la misma dirección MAC de la plantilla, nos aseguramos de que cada clon regenerara su propia MAC durante el proceso de clonación.
* Tras verificar con `ping 192.168.56.152` que se veían entre ellas correctamente, tomamos la instantánea `recien-clonada` en ambas.

---

## Ejercicio A1.3: Acceso y entorno de desarrollo

**Proceso de configuración SSH y comandos principales:**
1. Generamos las claves en nuestros equipos físicos locales usando:
   `ssh-keygen -t rsa`
2. Extrajimos la clave pública en Windows mediante `Get-Content ~/.ssh/id_rsa.pub` en PowerShell.
3. En la máquina virtual `dev.grupo7`, preparamos el directorio y el archivo de claves autorizadas:
   `mkdir -p ~/.ssh && chmod 700 ~/.ssh`
   `nano ~/.ssh/authorized_keys`
4. Tras verificar el acceso sin clave de los tres miembros procedimos a desactivar el acceso por contraseña clásica en `nano /etc/ssh/sshd_config` cambiando a `PasswordAuthentication no`, aplicando el cambio con `systemctl restart ssh`.
5. Instalamos el entorno necesario y levantamos la API:
   `apt install -y git php-cli php-mysql mariadb-server`
   `git clone https://github.com/esrora/tablon-avisos.git`
   `php -S 0.0.0.0:8000`

