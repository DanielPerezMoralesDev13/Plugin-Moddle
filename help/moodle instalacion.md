# Para instalar Moodle en un sistema Ubuntu, sigue estos pasos detallados

## Paso 1: Actualizar el sistema

Primero, actualiza los paquetes de tu sistema para asegurarte de que tienes las versiones más recientes de los paquetes instalados:

```bash
sudo apt update
sudo apt upgrade -y
```

### Paso 2: Instalar Apache, MySQL y PHP

Moodle requiere un servidor web (Apache), una base de datos (MySQL o MariaDB) y PHP. Instala estos componentes con el siguiente comando:

```bash
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql php-xml php-intl php-zip php-gd php-curl php-xmlrpc php-soap php-mbstring php-bcmath -y
```

### Paso 3: Configurar MySQL

Asegúrate de que MySQL esté seguro y crea una base de datos para Moodle:

1. Ejecuta el script de seguridad de MySQL:

    ```bash
    sudo mysql_secure_installation
    ```

    Sigue las instrucciones en pantalla para configurar la seguridad de MySQL.

2. Inicia sesión en MySQL:

    ```bash
    sudo mysql -u root -p
    ```

3. Crea la base de datos y el usuario para Moodle:

    ```sql
    CREATE DATABASE moodle DEFAULT CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
    CREATE USER 'moodleuser'@'localhost' IDENTIFIED BY 'password';
    GRANT ALL PRIVILEGES ON moodle.* TO 'moodleuser'@'localhost';
    FLUSH PRIVILEGES;
    EXIT;
    ```

Asegúrate de cambiar `'password'` por una contraseña segura.

### Paso 4: Descargar y configurar Moodle

Descarga Moodle desde su sitio oficial y configura los permisos necesarios:

1. Ve al directorio `/var/www/html`:

    ```bash
    cd /var/www/html
    ```

2. Descarga Moodle (puedes ajustar la versión según sea necesario):

    ```bash
    sudo wget https://download.moodle.org/stable39/moodle-latest-39.tgz
    ```

3. Extrae el archivo descargado:

    ```bash
    sudo tar -xvzf moodle-latest-39.tgz
    ```

4. Configura los permisos:

```bash
sudo chown -R www-data:www-data /var/www/html/moodle
sudo chmod -R 755 /var/www/html/moodle
```

### Paso 5: Crear el directorio de datos de Moodle

Crea un directorio para los datos de Moodle fuera del directorio web para mayor seguridad:

```bash
sudo mkdir /var/moodledata
sudo chown -R www-data:www-data /var/moodledata
sudo chmod -R 755 /var/moodledata
```

### Paso 6: Configurar Apache para Moodle

Configura Apache para servir Moodle correctamente:

1. Crea un archivo de configuración para Moodle:

    ```bash
    sudo nano /etc/apache2/sites-available/moodle.conf
    ```

2. Agrega la siguiente configuración al archivo:

    ```apache
    <VirtualHost *:80>
        ServerAdmin admin@example.com
        DocumentRoot /var/www/html/moodle
        ServerName example.com
        ServerAlias www.example.com

        <Directory /var/www/html/moodle>
            Options +FollowSymlinks
            AllowOverride All
            Require all granted
        </Directory>

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined

        <Directory /var/moodledata>
            Require all denied
        </Directory>
    </VirtualHost>
    ```

    Asegúrate de reemplazar `example.com` con tu nombre de dominio o dirección IP.

3. Habilita el sitio de Moodle y mod_rewrite:

    ```bash
    sudo a2ensite moodle.conf
    sudo a2enmod rewrite
    ```

4. Reinicia Apache para aplicar los cambios:

```bash
sudo systemctl restart apache2
```

### Paso 7: Completar la instalación de Moodle

Finalmente, completa la instalación de Moodle desde el navegador web. Abre tu navegador y ve a `http://example.com` (reemplaza `example.com` con tu dominio o dirección IP). Sigue las instrucciones en pantalla para completar la configuración de Moodle.

¡Y eso es todo! Ahora deberías tener Moodle instalado y funcionando en tu servidor Ubuntu.
