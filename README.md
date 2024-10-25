# 1. Utilizar la imagen de Ubuntu, tag 22 e instalar LAMP en dicho contenedor.
## Preparativos
- **Descargo la imagen de ubuntu con el tag 22.04**   
<code>docker image pull ubuntu:22.04</code>

- **Creo e inicializo el contenedor**   
<code>docker run -it --name ubuntu22 -p 8000:80 ubuntu:22.04 /bin/bash</code>       

- **Actualizo los paquetes**       
<code>apt update</code>

- **Instalo systemctl porque lo voy a tener que utilizar más tarde**   
<code>apt install systemctl</code>

## Instalaciones principales
- **Instalo Apache2**   
<code>apt install -y apache2 apache2-utils</code>

- **Instalo MariaDB**   
<code>apt install -y mariadb-server mariadb-client</code>

- **Inicializo mariadb para poder utilizar los comandos de mysql**   
<code>service mariadb start</code>

- **Aseguro la instalación de mysql**   
<code>mysql_secure_installation</code>

- **Instalo PHP**    
<code>apt install -y php php-mysql libapache2-mod-php</code>

- **Reinicio apache2**   
<code>systemctl restart apache2</code>

## Comprobaciones

- **Creo el archivo de información de PHP para poder comprobarlo desde el navegador**   
<code>echo "<?php phpinfo(); ?>" | tee /var/www/html/info.php</code>

- **Compruebo PHP desde el navegador**   
![image](https://github.com/user-attachments/assets/cbfdf0ac-783b-497a-8a11-bd82b67b401f)

# 2. Instalar wordpress en el contenedor.
## Preparativos
- **Actualizar e instalar dependencias**
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

## Instalaciones principales
- **Descargar e instalar WordPress**

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

## Configuración
- **Configurar Apache2**
  
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

Activo el sitio web   
<code>a2ensite wordpress</code>

Habilitar la reescritura de URL   
<code>a2enmod rewrite</code>

Desactivar la página por defecto   
<code>a2dissite 000-default</code>

Reinicio apache2 para que se apliquen los cambios   
<code>service apache2 reload</code>

- **Configurar la base de datos**

Creo la base de datos, usuario y le doy los privilegios necesarios
~~~
mysql -u root
CREATE DATABASE wordpress;
CREATE USER enrique IDENTIFIED BY 'admin';
GRANT SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER ON wordpress.* TO enrique;
FLUSH PRIVILEGES;
quit
~~~

Activar la base de datos   
<code>service mysql start</code>

# 3. Configuración y comprobación final
 - **Acceder a wordpress desde el navegador**
![image](https://github.com/user-attachments/assets/3b4f09bb-a143-4d3f-a0f6-4fadd8de5078)

- **Crear el archivo wp-config.php**
Dentro se pone lo siguiente
~~~
<?php
/**
 * The base configuration for WordPress
 *
 * The wp-config.php creation script uses this file during the installation.
 * You don't have to use the website, you can copy this file to "wp-config.php"
 * and fill in the values.
 *
 * This file contains the following configurations:
 *
 * * Database settings
 * * Secret keys
 * * Database table prefix
 * * ABSPATH
 *
 * @link https://developer.wordpress.org/advanced-administration/wordpress/wp-config/
 *
 * @package WordPress
 */

// ** Database settings - You can get this info from your web host ** //
/** The name of the database for WordPress */
define( 'DB_NAME', 'wordpress' );

/** Database username */
define( 'DB_USER', 'enrique' );

/** Database password */
define( 'DB_PASSWORD', 'admin' );

/** Database hostname */
define( 'DB_HOST', 'localhost' );

/** Database charset to use in creating database tables. */
define( 'DB_CHARSET', 'utf8mb4' );

/** The database collate type. Don't change this if in doubt. */
define( 'DB_COLLATE', '' );

/**#@+
 * Authentication unique keys and salts.
 *
 * Change these to different unique phrases! You can generate these using
 * the {@link https://api.wordpress.org/secret-key/1.1/salt/ WordPress.org secret-key service}.
 *
 * You can change these at any point in time to invalidate all existing cookies.
 * This will force all users to have to log in again.
 *
 * @since 2.6.0
 */
define( 'AUTH_KEY',         'e:+fM>)=pA~rbHU?d(o;s1#av>DRj%fNtTt#=C>y;XlH[$nFfOH6(;Yxkwa.7jmi' );
define( 'SECURE_AUTH_KEY',  'N K<*n2H:^mECci^^q99UUwsH~9 JLnZn=%$#+67xVHlT0AVD1JB~M&t#HJG)=?9' );
define( 'LOGGED_IN_KEY',    'u rW>~E[iu[qXO2%c6DTGa@^vc7Lsc3df(obw]Gr9#T.tn{u0!X*^Yb{/j(ubT`I' );
define( 'NONCE_KEY',        'cJ_h%7P6wqyUazB:.dGT_u26rL1tKKBRM%^w:=31yfCV%@8t%Lj/yn=Dk4u>?mqK' );
define( 'AUTH_SALT',        '3|sW}$%I%kj:_(4;l&qHCQ8{d8LSB-}aoBdl `X_k`@S=e]V]OF0c+{M#y?S.XoB' );
define( 'SECURE_AUTH_SALT', '&XJQ&)OrxI#AB:+2y-K[tUF<BW>d(cCwI~d}r~;R[HB)V&1{DZ7TNTBIdjBuPzw-' );
define( 'LOGGED_IN_SALT',   'W,D#!~4yMGN:p,]V12.bVpM8#il~@<9|bzqpB<qquc~hN-*N?dOY+|uW*2iR#I9R' );
define( 'NONCE_SALT',       'iieWDF_L)#wfhl@Fx&MuKt#c19Wt:]JE&RDL0u{Wbn=cak0WyUn&?H@ENB E@,P?' );

/**#@-*/

/**
 * WordPress database table prefix.
 *
 * You can have multiple installations in one database if you give each
 * a unique prefix. Only numbers, letters, and underscores please!
 */
$table_prefix = 'wp_';

/**
 * For developers: WordPress debugging mode.
 *
 * Change this to true to enable the display of notices during development.
 * It is strongly recommended that plugin and theme developers use WP_DEBUG
 * in their development environments.
 *
 * For information on other constants that can be used for debugging,
 * visit the documentation.
 *
 * @link https://developer.wordpress.org/advanced-administration/debug/debug-wordpress/
 */
define( 'WP_DEBUG', false );

/* Add any custom values between this line and the "stop editing" line. */



/* That's all, stop editing! Happy publishing. */

/** Absolute path to the WordPress directory. */
if ( ! defined( 'ABSPATH' ) ) {
        define( 'ABSPATH', __DIR__ . '/' );
}

/** Sets up WordPress vars and included files. */
require_once ABSPATH . 'wp-settings.php';
~~~

- **Accedo a la base de datos desde el navegador**
![image](https://github.com/user-attachments/assets/6b520843-d4e9-4c4a-9cc5-b76175f6e702)
![image](https://github.com/user-attachments/assets/a05a9577-bf0b-4984-8c78-d7bd14d46f03)


