# 1. Utilizar la imagen de Ubuntu, tag 22 e instalar LAMP en dicho contenedor.
## Preparativos
- Descargo la imagen de ubuntu con el tag 22.04    
<code>docker image pull ubuntu:22.04</code>

- Creo y ejecuto el contenedor.   
<code>docker run -it --name ubuntu22 -p 8000:80 ubuntu:22.04 /bin/bash</code>       

- Actualizo los paquetes.       
<code>apt update</code>

- Instalo systemctl porque lo voy a tener que utilizar más tarde.   
<code>apt install systemctl</code>

## Instalaciones principales
- Instalo Apache2   
<code>apt install -y apache2 apache2-utils</code>

- Instalo MariaDB  
<code>apt install -y mariadb-server mariadb-client</code>

- Inicializo mariadb para poder utilizar los comandos de mysql   
<code>service mariadb start</code>

- Aseguro la instalación de mysql   
<code>mysql_secure_installation</code>

- Instalo PHP    
<code>apt install -y php php-mysql libapache2-mod-php</code>

- Reinicio apache2   
<code>systemctl restart apache2</code>

## Comprobaciones

- Creo el archivo de información de PHP para poder comprobarlo desde el navegador.   
<code>echo "<?php phpinfo(); ?>" | tee /var/www/html/info.php</code>

- Compruebo PHP desde el navegador.   
![image](https://github.com/user-attachments/assets/cbfdf0ac-783b-497a-8a11-bd82b67b401f)

# 2. Instalar wordpress en el contenedor.
## Preparativos
- Actualizar e instalar dependencias
~~~
apt update
apt install apache2 \
                 ghostscript \
                 libapache2-mod-php \
                 mysql-server \
                 php \
                 php-bcmath \
                 php-curl \
                 php-imagick \
                 php-intl \
                 php-json \
                 php-mbstring \
                 php-mysql \
                 php-xml \
                 php-zip
~~~

- Descargar e instalar WordPress

Creo el directorio y pongo como propietario a www-data
~~~
mkdir -p /srv/www
chown www-data: /srv/www
~~~

Descargo y descomprimo la última actualización de wordpress y la copio a /srv/www
~~~
curl -O https://wordpress.org/latest.tar.gz
tar zx -C /srv/www -f latest.tar.gz
~~~

- Configurar Apache2
Creo el archivo de configuración de WordPress y lo modifico
<code>touch /etc/apache2/sites-available/wordpress.conf</code>
~~~
<VirtualHost *:80>
    DocumentRoot /srv/www/wordpress
    <Directory /srv/www/wordpress>
        Options FollowSymLinks
        AllowOverride Limit Options FileInfo
        DirectoryIndex index.php
        Require all granted
    </Directory>
    <Directory /srv/www/wordpress/wp-content>
        Options FollowSymLinks
        Require all granted
    </Directory>
</VirtualHost>
~~~

<code>a2ensite wordpress</code>
<code>service apache2 reload</code>

<code>a2enmod rewrite</code>
<code>service apache2 reload</code>

<code>a2dissite 000-default</code>
<code>service apache2 reload</code>
