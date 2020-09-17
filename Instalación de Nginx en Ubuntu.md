# Instalación de Nginx en Ubuntu

**Nginx** en simples palabras es un servidor web, *proxy inverso* y balanceador de carga open source que hoy por hoy soporta a millones de páginas web en todo el mundo.

![](https://kinsta.com/es/wp-content/uploads/sites/8/2018/03/qué-es-nginx-1.png)



## Instalación de Nginx

La primera acción será actualizar la información de estos repositorios, e incluso los paquetes ya instalados, que es la práctica habitual antes de instalar nuevo software en el sistema:

```bash
sudo apt update && sudo apt -y upgrade
```

Descargar e instalar el paquete *nginx* junto a todas sus dependencias:

```bash
sudo apt -y install nginx
```



## Controlar Servicio Nginx

Para detener el servidor:

```bash
sudo systemctl stop nginx
```

Iniciar el servicio:

```bash
sudo systemctl start nginx
```

Reiniciar el servicio:

```bash
sudo systemctl restart nginx
```

Volver a cargarlo todo después de realizar algunos cambios de configuración:

```bash
sudo systemctl reload nginx
```

Deshabilitar el servidor:

```bash
sudo systemctl disable nginx
```

Habilitar de nuevo con el comando:

```bash
sudo systemctl enable nginx
```



## Configurar el Firewall

```bash
sudo ufw allow 'Nginx HTTP'
```



## Instalar PHP

```bash
sudo apt-get install php-fpm php-mysql
```

```bash
sudo vim /etc/php/7.3/fpm/php.ini
```

```bash
sudo systemctl restart php7.3-fpm
```



## Configurar Nginx

### Servidores virtuales de Nginx

Al usar el servidor web Nginx, se pueden usar los *bloques del servidor* (parecidos a los hosts virtuales en Apache) para encapsular los detalles de configuración y alojar más de un dominio desde un solo servido

Creamos el directorio donde se alojarán los archivos del sitio web.

```bash
sudo mkdir -p /var/www/$example.com/html
```

Dar permisos de escritura.

```bash
sudo chown -R $USER:$USER /var/www/example.com/html
```

Crear una pagina index.html

```bash
vim /var/www/$example.com/html/index.html
```

Para que Nginx lproporcione servicios a este contenido, se debe crear un bloque del servidor usando las directivas correctas.

```bash
sudo nano /etc/nginx/sites-available/$example.com
```

```
server {
        listen 80;
        listen [::]:80;

        root /var/www/$example.com/html;
        index index.html index.htm index.nginx-debian.html;

        server_name $example.com www.$example.com;

        location / {
                try_files $uri $uri/ =404;
        }
        
        location ~ \.php$ {
        		include snippets/fastcgi-php.conf;
        		fastcgi_pass unix:/run/php/php7.0-fpm.sock;
    	}

    	location ~ /\.ht { 
        	deny all;
    	]
}
```

Habilitar el archivo creando un enlace desde el mismo al directorio `sites-enabled` (habilitado para sitios), el cual Nginx usa para leer durante el inicio.

```bash
sudo ln -s /etc/nginx/sites-available/$example.com /etc/nginx/sites-enabled/
```

Eliminar la pagina por default

```bash
sudo rm /etc/nginx/sities-enabled/default
```

Para evitar un posible problema de memoria de hash bucket,

```bash
sudo vim /etc/nginx/nginx.conf
```

```
http {
	server_names_hash_bucket_size 64
}
```

# Fuentes:

[Ubunlog](https://ubunlog.com/nginx-instala-servidor-ubuntu/)

[Linode](https://www.linode.com/docs/web-servers/nginx/how-to-configure-nginx/)

[ComoInstalar](https://comoinstalar.me/como-instalar-nginx-en-ubuntu-18-04/)

[DigitalOcean](https://www.digitalocean.com/community/tutorials/como-instalar-nginx-en-ubuntu-18-04-es)

[Digital](https://www.digitalocean.com/community/tutorials/como-instalar-linux-nginx-mysql-php-lemp-stack-in-ubuntu-16-04-es)

[Atareao](https://www.atareao.es/tutorial/raspberry-pi-primeros-pasos/infraestructura-lemp-con-nginx-en-raspberry/)