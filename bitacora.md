## Punto de partida

### Ejercicio A1.1: La lista de las siete carencias

1. Se utiliza ROOT y no requiere contraseña.
   - **Consecuencia con usuarios reales:** Cualquier persona o atacante podría acceder a la base de datos con privilegios totales, permitiendo la lectura, modificación o borrado malintencionado de todos los datos de los usuarios sin ningún obstáculo de seguridad.

2. Hay que iniciar el panel de control de Xampp, abrir el servidor de Apache y mySQL y abrir la URL del proyecto (http://localhost/proyectoMercado/index.php).
   - **Consecuencia con usuarios reales:** El servicio depende de una intervención manual; si el servidor se reinicia por un fallo eléctrico o actualización, la aplicación quedará fuera de servicio indefinidamente hasta que un administrador inicie los servicios a mano, generando caídas prolongadas para los usuarios.

3. Queda registrado en la ruta C:\xampp\php\logs\php_error_log.
   - **Consecuencia con usuarios reales:** Al estar los logs almacenados en local de forma estática en una sola máquina sin rotación ni monitorización centralizada, es imposible detectar ataques en tiempo real o auditar fallos del sistema antes de que afecten gravemente a los usuarios.

4. Mi versión es la php 8.2.12 y la de mi compañero es la misma.
   - **Consecuencia con usuarios reales:** Al depender de versiones instaladas manualmente en entornos locales sin aislamiento (como Docker), cualquier desajuste o actualización no controlada en el servidor de producción puede romper la compatibilidad y provocar errores inesperados a los usuarios.


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

