# Practica-iaw-1.3
Repositorio para la práctica 1.3. de IAW

Antonio Jesús Gálvez Rodríguez 2º A.S.I.R

# 1 Archivo `install_lamp.sh`
En esta práctica vamos a aprender cómo manejar diferentes herramientas que contamos como Apache o phpMyAdmin para manejar nuestro sitio web, lo primero que haremos será crear una primera carpeta llamada `scripts` donde crearemos un archivo llamado `install_lamp.sh` donde instalaremos la pila LAMP.

En la cabecera de nuestro archivo creado pondremos lo siguiente:
```
#!/bin/bash
```
Esto indica que el script debe ejecutarse con el intérprete de Bash e irá siempre en la cabecera del archivo.
```
set -ex
```
Este comando hace que el script muestre cada comando antes de ejecutarlo `(-x)` y salga si algún comando falla `(-e)`.

## 1.1 Actualización del Sistema
### 1.1.1 Actualizar los repositorios
 
 ```
apt update
```
Actualiza la lista de paquetes disponibles en los repositorios configurados.

### 1.1.2 Actualizar los paquetes
```
apt upgrade -y
```
Actualiza todos los paquetes instalados a sus versiones más recientes. El `-y` automáticamente acepta todas las confirmaciones.

## 1.2 Instalación de Apache
### 1.2.1 Instalar el servidor web Apache
```
apt install apache2 -y
```
Instala el servidor web Apache. El -y hace lo mismo que se ha explicado anteriormente

### 1.2.1.1 Habilitar el módulo rewrite de Apache
```
a2enmod rewrite
```
Habilita el módulo rewrite de Apache, que es útil para la reescritura de URL's.

### 1.2.2 Creación del archivo de configuración de Apache
Crearemos una carpeta llamada `conf` en la que crearemos un primer archivo `000-default.conf` donde incluiremos el siguiente contenido:
```
<VirtualHost *:80>
    #ServerName www.example.com
    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/html/


    DirectoryIndex index.php index.html


    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
### 1.2.2.1 Funcionamiento del archivo
`<VirtualHost *:80>`
Nos indica que este bloque de configuración se aplicará a las solicitudes HTTP en el puerto 80.

`# ServerName www.example.com`
Esta línea está comentada y no se ejecuta, pero puede ser usada para especificar el nombre del servidor (dominio).

`ServerAdmin webmaster@localhost`
Define la dirección del administrador del sitio. Los errores del servidor podrían enviarse a esta dirección.

`DocumentRoot /var/www/html/`
Especifica el directorio raíz desde el cual se servirán los archivos. Aquí es donde Apache buscará los archivos para darlos a los visitantes.

`DirectoryIndex index.php index.html`
Define la lista de archivos que Apache buscará y servirá como página predeterminada en un directorio. En este caso, buscará index.php primero, y si no lo encuentra, buscará index.html.

`ErrorLog ${APACHE_LOG_DIR}/error.log`
Establece la ubicación del archivo de registro de errores. ${APACHE_LOG_DIR} es una variable que apunta al directorio de logs de Apache.

`CustomLog ${APACHE_LOG_DIR}/access.log combined`
Define la ubicación del archivo de registro de accesos y el formato del log. combined es un formato de registro que incluye información detallada sobre cada solicitud.

### 1.2.2.2 Copiar archivo de configuración de Apache
```
cp ../conf/000-default.conf /etc/apache2/sites-available
```
Con esto vamos a conseguir copiar el archivo de configuración personalizado de Apache al directorio donde Apache busca sus configuraciones de sitios disponibles.

## 1.3. Instalación de PHP
### 1.3.1 Instalar PHP y los módulos de PHP para Apache y MySQL
```
apt install php libapache2-mod-php php-mysql -y
```
Instala PHP y los módulos necesarios para que Apache pueda procesar scripts PHP y para que PHP pueda interactuar con MySQL.

## 1.3.2 Reiniciar Apache
### 1.3.2.1 Reiniciar el servicio de Apache
```
systemctl restart apache2
```
Reinicia el servicio de Apache para aplicar los cambios de configuración y cargar los nuevos módulos instalados.

## 1.4. Instalación de MySQL
### 1.4.1 Instalar MySQL Server
```
apt install mysql-server -y
```
Instala el servidor de base de datos MySQL.

### 1.4.1.1 Configuración Adicional
### 1.4.1.2 Copiar el script de prueba de PHP a `/var/www/html`
```
cp ../php/index.php /var/www/html
```

Copia un script de prueba PHP a la raíz del servidor web, permitiendo verificar que PHP está funcionando correctamente.

### 1.4.1.3 Modificar el propietario y el grupo del archivo `index.php`
```
chown -R www-data:www-data /var/www/html
```
Cambia el propietario y el grupo del archivo index.php y de todos los archivos en el directorio /var/www/html a www-data, que es el usuario y grupo predeterminados de Apache.

# 2 Archivo `install_tools.conf`
## 2.1 Configuración inicial del archivo
En la carpeta `scripts`crearemos otro archivo creado `install_tools.conf` con el siguiente contenido:
```  
#!/bin/bash

set -ex

apt update

apt upgrade -y
```
Los primeros comandos que pondremos serán idénticos a los del archivo anterior y que, como ya explicamos, serán fundamentales para los comandos que irán posteriormente.

## 2.2 Configuración de phpMyAdmin
### 2.2.1 Configurar las respuestas para la instalación de phpMyAdmin
```
echo "phpmyadmin phpmyadmin/reconfigure-webserver multiselect apache2" | debconf-set-selections
echo "phpmyadmin phpmyadmin/dbconfig-install boolean true" | debconf-set-selections
echo "phpmyadmin phpmyadmin/mysql/app-pass password $PHPMYADMIN_APP_PASSWORD" | debconf-set-selections
echo "phpmyadmin phpmyadmin/app-password-confirm password $PHPMYADMIN_APP_PASSWORD" | debconf-set-selections
```
Estos comandos configuran las respuestas para el proceso interactivo de instalación de phpMyAdmin, esto permitirá que toda la instalación sea automática y que no necesitemos de una persona respondiendo.

## 2.3 Instalar phpMyAdmin
```
sudo apt install phpmyadmin php-mbstring php-zip php-gd php-json php-curl -y
```
Instala phpMyAdmin junto con algunos módulos PHP adicionales necesarios.

### 2.3.1 Archivo `index.php`
Crearemos una carpeta llamada `php` donde incluiremos el archivo `index.php` con el siguiente contenido.
```
<?php

phpinfo();

?>
```
Al acceder a la pagina web veremos el siguiente contenido:

![](/IMG/Captura%20de%20pantalla%202024-10-18%20190908.png)

## 2.4 Instalación de Adminer
### Paso 1: Crear un directorio para Adminer
```
mkdir -p /var/www/html/adminer
```
Crea el directorio donde se aloja Adminer.

### Paso 2: Descargar Adminer
```
wget https://github.com/vrana/adminer/releases/download/v4.8.1/adminer-4.8.1-mysql.php -P /var/www/html/adminer
```
Descarga el script de Adminer a la carpeta creada.

### Paso 3: Renombrar el script de Adminer
```
mv /var/www/html/adminer/adminer-4.8.1-mysql.php /var/www/html/adminer/index.php
```
Renombra el script descargado a index.php para facilitar el acceso al archivo.

### Paso 4: Modificar el propietario y grupo del archivo
```
chown -R www-data:www-data /var/www/html/adminer
```
Cambia el propietario y grupo del directorio de Adminer a www-data, el usuario y grupo predeterminados de Apache.

##  2.5 Instalación de GoAccess
### 2.5.1 Instalar GoAccess
```
apt install goaccess -y
```
Instala GoAccess, una herramienta de análisis de registros de acceso de Apache.

### 2.5.1.1 Crear un directorio para los informes estadísticos
```
mkdir -p /var/www/html/stats
```
Crea un directorio donde se almacenarán los informes generados por GoAccess.

### 2.5.2 Ejecutar GoAccess en segundo plano
```
goaccess /var/log/apache2/access.log -o /var/www/html/stats/index.html --log-format=COMBINED --real-time-html --daemonize
```
Ejecuta GoAccess en segundo plano para generar informes en tiempo real basados en los registros de acceso de Apache.
