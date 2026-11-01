## Punto de partida

### Ejercicio A1.1: La lista de las siete carencias

1. Se utiliza ROOT y no requiere contraseña.
   - **Consecuencia con usuarios reales:** Cualquier atacante podría acceder a la base de datos con privilegios totales, permitiendo la lectura, modificación o borrado de todos los datos de los usuarios.

2. Hay que iniciar el panel de control de Xampp, abrir el servidor de Apache y mySQL y abrir la URL del proyecto (http://localhost/proyectoMercado/index.php).
   - **Consecuencia con usuarios reales:** Si el servidor se reinicia por un fallo eléctrico, la aplicación quedará caída indefinidamente hasta que alguien intervenga de forma manual.

3. Queda registrado en la ruta C:\xampp\php\logs\php_error_log.
   - **Consecuencia con usuarios reales:** Al estar los logs guardados de forma estática en una sola máquina sin monitorización, es imposible detectar errores o brechas de seguridad a tiempo.

4. Mi versión es la php 8.2.12 y la de mi compañero es la misma.
   - **Consecuencia con usuarios reales:** Al depender de instalaciones locales manuales sin aislamiento, cualquier actualización imprevista puede romper la compatibilidad y dejar la aplicación inaccesible.


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

## Ejercicio A1.4: Endurecimiento y preparación del entorno de producción

**Proceso de configuración y comandos principales:**
1. Sobre la máquina virtual pro.grupo7, creamos el usuario de servicio dedicado al sistema, sin carpeta personal ni intérprete de órdenes, comprobando que se deniega el inicio de sesión:
   `sudo useradd -r -s /usr/sbin/nologin tablonsu - tablon`

2. Creamos el directorio exclusivo de la aplicación asignando al usuario de servicio como propietario:
   `sudo mkdir -p /opt/tablonsudo chown -R tablon:tablon /opt/tablon`
   
3. Instalamos y configuramos el cortafuegos ufw estableciendo una política restrictiva y permitiendo exclusivamente los puertos de administración y tráfico web:
   `sudo apt update && sudo apt install -y ufw`
   `sudo ufw default deny incomingsudo ufw default allow outgoing`
   `sudo ufw allow 22/tcp && sudo ufw allow 80/tcp && sudo ufw allow 443/tcp`
   `sudo ufw enable`

4. Instalamos el servidor web Nginx y comprobamos que sirve la página de prueba por defecto en el puerto 80:
   `sudo apt install -y nginx`
   `sudo systemctl enable --now nginx`
   `curl -I http://localhost`

5. Verificamos desde la máquina dev.grupo7 que los puertos internos o de desarrollo de producción (como el puerto 3000) permanecen incomunicados y protegidos:
   `nc -zv 192.168.56.153 3000`
