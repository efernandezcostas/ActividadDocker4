# 1. Utilizar la imagen de Ubuntu, tag 22 e instalar LAMP en dicho contenedor.
## Preparativos
- Descargo la imagen de ubuntu con el tag 22.04    
<code>docker image pull ubuntu:22.04</code>

- Creo y ejecuto el contenedor.   
<code>docker run -it --name ubuntu22 -p 8000:80 ubuntu22:04 /bin/bash</code>       

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
