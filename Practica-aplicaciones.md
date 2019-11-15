# Tarea 3) Instalación aplicaciones web

Vamos a instalar dos aplicaciones web php en nuestros servidores:
- En www.tunombre.gonzalonazareno.org vamos a instalar WordPress. En WordPress debemos configurar de forma correcta las URL limpias.
- En cloud.tunombre.gonzalonazareno.org vamos a instalar NextCloud.

Modifica las aplicaciones web y personalizalas para que se demuestre que son tus aplicaciones. Entrega una breve descripción de los pasos dados para conseguir la instalación de las aplicaciones web. Usando resolución estática entrega algunas capturas donde se demuestre que las aplicaciones están funcionando.

## Actualización de php
En ejercicios anteriores se ha descargado php y sus extensiones en la versión 5.4. Hay que actualizarlo todo a la versión 7.2 de la siguiente manera.

Se borran las versiones anteriores, agregan los repositorios necesarios y se instalan.



## Instalación de WordPress en Centos
Se descargan los paquetes que vamos a necesitar para la instalación de WordPress:
~~~
[centos@salmorejo ~]$ sudo yum install wget
[centos@salmorejo ~]$ sudo yum install unzip
~~~

Y se inicia la descarga de wordpress y se descomprime:
~~~
[centos@salmorejo ~]$ wget https://es.wordpress.org/latest-es_ES.zip
[centos@salmorejo ~]$ unzip latest-es_ES.zip 
~~~

Y se modifica el fichero de configuración de ngix /etc/ngix/conf.d/default.conf:
~~~
server {
    listen	 80;
    server_name  www.paloma.gonzalonazareno.org;

    # note that these lines are originally from the "location /" block
    root   /usr/share/nginx/html/wordpress;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html/wordpress;
    }

    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
~~~


## Configuración de selinux
Selinux no permite el acceso al instalador de wordpress desde un navegador, por lo tanto hay que introducir los siguientes comandos para permitir el acceso:
~~~
[centos@salmorejo ~]$ sudo chown nginx:nginx -R /usr/share/nginx/html/wordpress
[centos@salmorejo ~]$ sudo find /usr/share/nginx/html/wordpress -type f -exec chmod 0644 {} \;
[centos@salmorejo ~]$ sudo find /usr/share/nginx/html/wordpress -type d -exec chmod 0755 {} \;
[centos@salmorejo ~]$ sudo chcon -t httpd_sys_content_t /usr/share/nginx/html/wordpress -R
[centos@salmorejo ~]$ sudo chcon -t httpd_sys_rw_content_t /usr/share/nginx/html/wordpress/wp-config-sample.php 
[centos@salmorejo ~]$ sudo chcon -t httpd_sys_rw_content_t /usr/share/nginx/html/wordpress/wp-content -R
[centos@salmorejo ~]$ sudo systemctl restart nginx
~~~


