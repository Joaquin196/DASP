# Acceso y entorno de desarrollo (dev.grupo7)

### 1. Generación de claves e inserción en el servidor
En los equipos físicos de los miembros del grupo (Windows/PowerShell):
```bash
ssh-keygen -t rsa
Get-Content ~/.ssh/id_rsa.pub
```

En la máquina virtual `dev.grupo7` para autorizar las claves:
```bash
mkdir -p ~/.ssh && chmod 700 ~/.ssh
nano ~/.ssh/authorized_keys
# (Se pegaron las tres claves públicas en este archivo)
chmod 600 ~/.ssh/authorized_keys
```

### 2. Desactivación del acceso por contraseña
Tras comprobar que los tres miembros accedían correctamente mediante su clave RSA y manteniendo una sesión abierta de seguridad, modificamos la configuración del servicio SSH:
```bash
nano /etc/ssh/sshd_config
# Se modificó la línea: PasswordAuthentication no

systemctl restart ssh
```

### 3. Instalación de dependencias y base de datos
Instalación de los paquetes necesarios para el servidor web, PHP y la base de datos:
```bash
apt update
apt install -y git php-cli php-mysql mariadb-server
```

### 4. Clonado del proyecto
Descarga del repositorio oficial del tablón de avisos:
```bash
git clone [https://github.com/esrora/tablon-avisos.git](https://github.com/esrora/tablon-avisos.git)
```

### 5. Arranque de la API
Acceso al directorio del proyecto y despliegue del servidor interno de PHP apuntando al exterior:
```bash
cd tablon-avisos
php -S 0.0.0.0:8000
```

### Comprobación de la API
Captura de `/api/version` respondiendo desde la máquina `dev.grupo7`:
