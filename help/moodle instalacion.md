# Guía de instalación de Moodle en un sistema Ubuntu

## Índice

- [Guía de instalación de Moodle en un sistema Ubuntu](#guía-de-instalación-de-moodle-en-un-sistema-ubuntu)
  - [Índice](#índice)
    - [Para instalar Moodle en un sistema Ubuntu, sigue estos pasos detallados](#para-instalar-moodle-en-un-sistema-ubuntu-sigue-estos-pasos-detallados)
  - [Paso 1: Actualizar el sistema](#paso-1-actualizar-el-sistema)
  - [Paso 2: Instalar Apache, MySQL y PHP](#paso-2-instalar-apache-mysql-y-php)
  - [Paso 3: Configurar MySQL](#paso-3-configurar-mysql)
  - [Paso 4: Descargar y configurar Moodle](#paso-4-descargar-y-configurar-moodle)
    - [Explicación de `chown` y `chmod` con la opción `-R` y el número `755`](#explicación-de-chown-y-chmod-con-la-opción--r-y-el-número-755)
      - [`chown` y la opción `-R`](#chown-y-la-opción--r)
      - [`chmod` y la opción `-R`](#chmod-y-la-opción--r)
    - [Explicación del número `755`](#explicación-del-número-755)
    - [Resumen](#resumen)
  - [Paso 5: Crear el directorio de datos de Moodle](#paso-5-crear-el-directorio-de-datos-de-moodle)
  - [Paso 6: Configurar Apache para Moodle](#paso-6-configurar-apache-para-moodle)
    - [Paso 1 Configuracion Apache: Recargar la configuración de Apache](#paso-1-configuracion-apache-recargar-la-configuración-de-apache)
    - [Paso 2 Configuracion Apache: Reiniciar Apache](#paso-2-configuracion-apache-reiniciar-apache)
    - [Resumen de comandos](#resumen-de-comandos)
  - [Paso 7: Completar la instalación de Moodle](#paso-7-completar-la-instalación-de-moodle)

---

### Para instalar Moodle en un sistema Ubuntu, sigue estos pasos detallados

## Paso 1: Actualizar el sistema

Primero, actualiza los paquetes de tu sistema para asegurarte de que tienes las versiones más recientes de los paquetes instalados:

```bash
sudo apt update
sudo apt upgrade -y
```

## Paso 2: Instalar Apache, MySQL y PHP

Moodle requiere un servidor web (Apache), una base de datos (MySQL o MariaDB) y PHP. Instala estos componentes con el siguiente comando:

```bash
sudo apt install apache2 mysql-server php libapache2-mod-php php-mysql php-xml php-intl php-zip php-gd php-curl php-xmlrpc php-soap php-mbstring php-bcmath -y
```

## Paso 3: Configurar MySQL

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

## Paso 4: Descargar y configurar Moodle

Descarga Moodle desde su sitio oficial y configura los permisos necesarios:

1. Ve al directorio `/var/www/html`:

    ```bash
    cd /var/www/html
    ```

2. Descarga Moodle (puedes ajustar la versión según sea necesario):

    ```bash
    sudo wget https://download.moodle.org/download.php/direct/stable404/moodle-latest-404.tgz
    ```

3. Extrae el fichero descargado:

    ```bash
    sudo tar -xvzf moodle-latest-404.tgz
    ```

4. Configura los permisos:

    ```bash
    sudo chown -R www-data:www-data /var/www/html/moodle
    sudo chmod -R 755 /var/www/html/moodle
    ```

### Explicación de `chown` y `chmod` con la opción `-R` y el número `755`

#### `chown` y la opción `-R`

El comando `chown` se usa para cambiar el propietario y el grupo de ficheros y directorios. La sintaxis básica es:

```bash
chown [propietario]:[grupo] [fichero o directorio]
```

En tu comando:

```bash
sudo chown -R www-data:www-data /var/www/html/moodle
```

- `sudo`: Ejecuta el comando con privilegios de superusuario.
- `chown`: Cambia el propietario de los ficheros.
- `-R`: Es una opción que significa "recursivo". Aplica el cambio de propietario a todos los ficheros y subdirectorios dentro del directorio especificado.
- `www-data:www-data`: Especifica que el propietario y el grupo serán `www-data`. Este es el usuario y grupo típicamente utilizado por el servidor web Apache en Ubuntu.
- `/var/www/html/moodle`: Es el directorio cuyo propietario y grupo deseas cambiar.

#### `chmod` y la opción `-R`

El comando `chmod` se usa para cambiar los permisos de ficheros y directorios. La sintaxis básica es:

```bash
chmod [permisos] [fichero o directorio]
```

En tu comando:

```bash
sudo chmod -R 755 /var/www/html/moodle
```

- `sudo`: Ejecuta el comando con privilegios de superusuario.
- `chmod`: Cambia los permisos de los ficheros.
- `-R`: Es una opción que significa "recursivo". Aplica el cambio de permisos a todos los ficheros y subdirectorios dentro del directorio especificado.
- `755`: Son los permisos que deseas establecer.
- `/var/www/html/moodle`: Es el directorio cuyos permisos deseas cambiar.

### Explicación del número `755`

Los permisos en Linux se representan con un número de tres dígitos, donde cada dígito puede ser un valor entre 0 y 7. Estos dígitos representan los permisos para el propietario, el grupo y otros (todos los demás usuarios).

- El primer dígito (`7`): Permisos para el propietario del fichero (en este caso, `www-data`).
- El segundo dígito (`5`): Permisos para el grupo (también `www-data`).
- El tercer dígito (`5`): Permisos para otros usuarios.

Cada dígito es la suma de los valores siguientes:

- `4`: Permiso de lectura (`r`)
- `2`: Permiso de escritura (`w`)
- `1`: Permiso de ejecución (`x`)

Entonces, `755` significa:

- `7` (4+2+1): Lectura, escritura y ejecución para el propietario.
- `5` (4+1): Lectura y ejecución para el grupo.
- `5` (4+1): Lectura y ejecución para otros.

### Resumen

- `sudo chown -R www-data:www-data /var/www/html/moodle`: Cambia recursivamente el propietario y el grupo de todos los ficheros y subdirectorios dentro de `/var/www/html/moodle` a `www-data`.
- `sudo chmod -R 755 /var/www/html/moodle`: Cambia recursivamente los permisos de todos los ficheros y subdirectorios dentro de `/var/www/html/moodle` a `755` (lectura, escritura y ejecución para el propietario; lectura y ejecución para el grupo y otros).

## Paso 5: Crear el directorio de datos de Moodle

Crea un directorio para los datos de Moodle fuera del directorio web para mayor seguridad:

```bash
sudo mkdir /var/moodledata
sudo chown -R www-data:www-data /var/moodledata
sudo chmod -R 755 /var/moodledata
```

## Paso 6: Configurar Apache para Moodle

Configura Apache para servir Moodle correctamente:

1. Crea un fichero de configuración para Moodle:

    ```bash
    sudo nano /etc/apache2/sites-available/moodle.conf
    ```

2. Agrega la siguiente configuración al fichero:

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

Cuando habilitas un nuevo sitio en Apache o activas un módulo, es necesario recargar o reiniciar Apache para que los cambios surtan efecto. Según el mensaje que has recibido, necesitas ejecutar los siguientes comandos:

### Paso 1 Configuracion Apache: Recargar la configuración de Apache

Para activar la nueva configuración del sitio, ejecuta:

```bash
sudo systemctl reload apache2
```

### Paso 2 Configuracion Apache: Reiniciar Apache

Para activar el módulo `rewrite`, ejecuta:

```bash
sudo systemctl restart apache2
```

### Resumen de comandos

1. Habilitar el sitio `moodle.conf`:

    ```bash
    sudo a2ensite moodle.conf
    ```

2. Habilitar el módulo `rewrite`:

    ```bash
    sudo a2enmod rewrite
    ```

3. Recargar la configuración de Apache:

    ```bash
    sudo systemctl reload apache2
    ```

4. Reiniciar Apache:

    ```bash
    sudo systemctl restart apache2
    ```

Una vez realizados estos pasos, Apache debería estar configurado correctamente para servir Moodle. A continuación, puedes proceder con la instalación de Moodle desde tu navegador web. Abre tu navegador y ve a `http://example.com` (reemplaza `example.com` con tu dominio o dirección IP) para completar la configuración de Moodle.

## Paso 7: Completar la instalación de Moodle

Finalmente, completa la instalación de Moodle desde el navegador web. Abre tu navegador y ve a `http://example.com` (reemplaza `example.com` con tu dominio o dirección IP). Sigue las instrucciones en pantalla para completar la configuración de Moodle.

¡Y eso es todo! Ahora deberías tener Moodle instalado y funcionando en tu servidor Ubuntu.
