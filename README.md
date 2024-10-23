# 1. Utilizar la imagen de Ubuntu, tag 22 e instalar LAMP en dicho contenedor.
- Descargo la imagen de ubuntu con el tag 22.04    
<code>docker image pull ubuntu:22.04</code>

- Creo y ejecuto el contenedor.   
<code>docker run -it --name ubuntu22 ubuntu22:04 /bin/bash</code>       

- Primero actualizo los paquetes.      
<code>apt update</code>

- Utilizo el comando para instalar todo (Apache, MariaDB y PHP) en un solo paso  
<code>apt install -y lamp-server^</code>
