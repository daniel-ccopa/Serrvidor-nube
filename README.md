# Implementación de un Servidor en la Nube

Esta guía proporciona los pasos necesarios para implementar un servidor en la nube utilizando Microsoft Azure como ejemplo, pero los principios se aplican a otras plataformas en la nube como AWS y Google Cloud.

## Tabla de Contenidos
1. [Requisitos Previos](#requisitos-previos)
2. [Creación de la Máquina Virtual](#creación-de-la-máquina-virtual)
3. [Conexión a la Máquina Virtual](#conexión-a-la-máquina-virtual)
4. [Instalación de Software](#instalación-de-software)
   - [Apache](#apache)
   - [MySQL](#mysql)
   - [PHP y PhpMyAdmin](#php-y-phpmyadmin)
5. [Configuración de Seguridad](#configuración-de-seguridad)
6. [Acceso y Administración de PhpMyAdmin](#acceso-y-administración-de-phpmyadmin)
7. [Consideraciones de Seguridad Adicionales](#consideraciones-de-seguridad-adicionales)

---

## Requisitos Previos
- Una cuenta en Microsoft Azure (o en otra plataforma de tu elección).
- Familiaridad básica con la línea de comandos.
- Herramienta de cliente SSH para conectarte a la VM (ej. OpenSSH en Linux/macOS o PuTTY en Windows).

## Creación de la Máquina Virtual

1. **Inicia sesión en Azure** y ve al portal de Azure.
2. Dirígete a **Crear un recurso** > **Máquina virtual**.
3. Configura los siguientes detalles:
   - **Grupo de recursos**: Crea uno nuevo o selecciona uno existente.
   - **Nombre de la VM**: Asigna un nombre a tu VM.
   - **Región**: Selecciona la ubicación de la VM (ejemplo: `East US`).
   - **Imagen**: Selecciona `Ubuntu Server 20.04 LTS`.
   - **Tamaño**: Selecciona el tamaño adecuado (ejemplo: `B1s` para aplicaciones de bajo consumo).
4. **Autenticación**:
   - Configura la autenticación con **clave SSH** o **contraseña**.
5. **Puertos de red**:
   - Asegúrate de habilitar el puerto **80 (HTTP)** y **443 (HTTPS)** para el tráfico web.
6. Haz clic en **Revisar y crear**, luego en **Crear** para desplegar la VM.

## Conexión a la Máquina Virtual

1. Una vez que la VM esté creada, ve a la sección de `Información general` de la VM y copia la **dirección IP pública**.
2. Abre una terminal y usa el siguiente comando para conectarte a la VM vía SSH:
   ```bash
   ssh nombre_usuario@ip_publica
   ```
3. Confirma la conexión escribiendo `yes` si es necesario.

## Instalación de Software

### Apache
1. Actualiza los paquetes del sistema:
   ```bash
   sudo apt update && sudo apt upgrade -y
   ```
2. Instala Apache2:
   ```bash
   sudo apt install apache2 -y
   ```
3. Verifica que Apache esté funcionando:
   ```bash
   sudo systemctl status apache2
   ```

### MySQL
1. Instala MySQL:
   ```bash
   sudo apt install mysql-server -y
   ```
2. Configura la seguridad de MySQL:
   ```bash
   sudo mysql_secure_installation
   ```
   - Sigue las indicaciones para establecer la seguridad básica de MySQL.

### PHP y PhpMyAdmin
1. Instala PHP junto con módulos necesarios:
   ```bash
   sudo apt install php libapache2-mod-php php-mysql -y
   ```
2. Instala PhpMyAdmin:
   ```bash
   sudo apt install phpmyadmin -y
   ```
   - Selecciona **Apache2** como el servidor web durante la instalación.
3. Configura Apache para que reconozca PhpMyAdmin:
   - Abre el archivo de configuración de Apache:
     ```bash
     sudo nano /etc/apache2/apache2.conf
     ```
   - Agrega la línea:
     ```plaintext
     Include /etc/phpmyadmin/apache.conf
     ```
   - Guarda el archivo y reinicia Apache:
     ```bash
     sudo systemctl restart apache2
     ```

## Configuración de Seguridad
1. En el portal de Azure, ve a la configuración de red de la VM y habilita los puertos **80**, **443** y **3306** (si necesitas acceso remoto a MySQL).
2. Usa `ufw` en la VM para gestionar el firewall:
   ```bash
   sudo ufw allow OpenSSH
   sudo ufw allow 'Apache Full'
   sudo ufw enable
   ```

## Acceso y Administración de PhpMyAdmin
1. En tu navegador, visita `http://ip_publica/phpmyadmin`.
2. Inicia sesión con el usuario y contraseña de MySQL configurados.

## Consideraciones de Seguridad Adicionales
- **Certificados SSL**: Para producción, instala certificados SSL para asegurar el tráfico en tu servidor.
- **Acceso SSH Seguro**: Utiliza claves SSH en lugar de contraseñas.
- **Actualizaciones**: Mantén el sistema y el software actualizados para evitar vulnerabilidades.
- **Backup**: Configura copias de seguridad automáticas de los datos y configuraciones importantes.

