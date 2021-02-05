# Servidor Proxy en Fedora

El servidor proxy generalmente guarda las páginas web visitadas para solicitudes posteriores, por lo que, si intentas visitar la misma página web o cualquier otra, la página se traerá del servidor proxy.

Un servidor proxy es un equipo dedicado que actúa como intermediario entre un dispositivo de punto final, como una computadora, y otro servidor del cual un usuario o cliente solicita un servicio. El servidor proxy puede existir en la misma máquina que un servidor de firewall o puede estar en un servidor independiente, que reenvía las solicitudes a través del firewall.

![](http://descargas.pntic.mec.es/mentor/visitas/demoSeguridadInformatica/Esquema-proxy-internet.png)

## ¿Qué es Squid?

Squid es un programa que te permite realizar un proxy/caché con una gran variedad de configuraciones y usos. Al usar una caché también consigue reducir el ancho de banda y los tiempo de respuesta gracias a su caché, y reutilizando las páginas web solicitadas con más frecuencia.



## Instalar Squid en derivados de Red Hat

```bash
sudo dnf -y install squid
```

Iniciar el servicio de **Squid** y habilitarlo para que se ejecute la iniciar el sistema.

```bash
sudo systemctl start squid
```

```bash
sudo systemctl enable squid
```

Hacer copia de seguridad.

```bash
sudo cp /etc/squid/squid.conf /etc/squid/squid.conf.bkp
```

Configuración del proxy en tu navegador a la dirección IP del proxy y el **puerto 3128**.



## Cambiar el puerto de acceso

El puerto 8888 en vez del 3128 que es el que viene por defecto, cualquier puerto > 1024 será válido.

```bash
sudo vim /etc/squid/squid.conf
```

```
http_port 8888
```



## Configurar la lista de control de acceso de Squid (ACL)

Permitir que los hosts en la red 192.168.56.0/24 accedan a Internet a través de nuestro servidor proxy, editar el archivo de configuración **/etc/squid/squid.conf** y agregar una entrada de ACL.

```
...
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#
acl internal src 19.168.56.0/24 # Allow Internal hosts
http_access allow internal
...
```

> Internal; el nombre puede variar, por ejemplo también se puede renombrar como localnet 
>
> acl **localnet** src 19.168.56.0/24
>
> http_access allow **localnet**



## Permitir una lista de IP’s en específicos

Crear archivo de texto con las distintas IPs.

```bash
sudo vim /etc/squid/allowed_ips.txt
```

```
192.168.1.1
xx.xx.xx.2
xx.xx.xx.3
xx.xx.xx.4
```

```
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#
acl allowed_ips src "/etc/squid/allowed_ips.txt"
http_access allow allowed_ips
...
```

> **allow permite**
>
> **deny deniega las ip's**
>
> http_access **deny** allowed_ips



## Permitir puertos locales especificos

```
acl piports port 21
acl piports port 22
http_access allow piports
```



## Bloquear sitios webs en específicos

Restringir el acceso a sitios web se debe crear un archivo que defina los dominios de estos sitios web.

```bash
sudo vim /etc/squid/restricted-sites.squid # Puede variar el nombre del archivo
```

```
.youtube.com
.facebook.com
.netflix.com
```

Después de eso, se crea una ACL para los sitios restringidos en el archivo de configuración **/etc/squid/squid.conf**.

```
...
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#
acl internal src 19.168.56.0/24 # Allow Internal hosts
acl blockedsites dstdomain "/etc/squid/restricted-sites.squid" # For restricted sites
http_access deny blockedsites
http_access allow internal
...
```



## Bloquear sitios basados en palabras claves específicas

```bash
sudo vim /etc/squid/keyword-ban.squid
```

```
porn
ads
movie
gamble
```

Realizar los cambios en el archivo de configuración de Squid

```
...
# INSERT YOUR OWN RULE(S) HERE TO ALLOW ACCESS FROM YOUR CLIENTS
#
acl internal src 19.168.56.0/24 # Allow Internal hosts
acl blockedsites dstdomain "/etc/squid/restricted-sites.squid" # For restricted sites
acl keyword-ban url_regex "/etc/squid/keyword-ban.squid"
http_access deny blockedsites
http_access deny keyword-ban
http_access allow internal
...
```



## Bloquear tipos de archivos

```
acl archivos urlpath_regex "/etc/squid/archivos"
http_access deny archivos
```

```
sudo vim /etc/squid/archivos
```

```
.ppt
.mp3$
.avi$
.mov$
```



## Limitar número de accesos

Limitar el uso simultaneo de accesos al proxy para limitar la conexión, limitar a tres conexiones.

```
acl maxuserconnections maxconn 3
```



## Caché de Squid

**Cache_dir** se utiliza para establecer el tamaño que queremos que use Squid del disco duro. De modo predeterminado se utiliza el formato **ufs** y se crea un directorio **/var/spool/squid** una caché de 100MB, que se divide en jerarquías de 16 directorios subordinados, hasta en 256 niveles cada uno.

```
cache_dir ufs /var/spool/squid 100 16 256
```

Aumentar el tamaño de la caché, cuanto más grande sea más objetos se almacenarán en esta, así se consumirá menos ancho de banda. Para una caché de 1GB.

```
cache_dir ufs /var/spool/squid 2048 16 256
```

El formato de caché **ufs** puede llegar a bloquear en Squid operaciones de entrada/salida sobre el sistema de archivos cuando se conectan muchos clientes. Es recomendable utilizar el sistema asíncrono **aufs** que consigue un mejor desempeño:

```
cache_dir aufs /var/spool/squid 2048 16 256
```

**Maximum_object_size** esta opción define el tamaño máximo de los objetos que se guardan en la caché, para una caché con límite de objetos de 48MB.

```
maximum_object_size 48 MB
```

**Cache_swap_low** y **cache_swap_high**. Cada vez que se llene la caché se puede automatizar la limpieza de esta cuando llegue a cierta cantidad. **cache_swap_low** establece el porcentaje a partir del cual se comenzará a limpiar la caché, y **cache_swap_high** establece el porcentaje en la que se limpiará la caché de manera agresiva. Para una caché que se empieza a limpiar al 90% y de manera agresiva al 95%:

```
cache_swap_low 90
cache_swap_high 95
```



## Navegación anónima

Por defecto Squid **reenvía la IP** del cliente que está conectado al proxy hacia el sitio web solicitado, pero para configurar de manera anónima hay que desactivar esta función y ocultar las IP’s de los clientes para mostrar la dirección IP que tenemos configurada en el servidor de Squid.

```
...
#
refresh_pattern ^ftp:           1440    20%     10080
refresh_pattern ^gopher:        1440    0%      1440
refresh_pattern -i (/cgi-bin/|\?) 0     0%      0
refresh_pattern .               0       20%     4320
# Anonymize Traffic
via off
forwarded_for off

request_header_access From deny all
request_header_access Server deny all
request_header_access WWW-Authenticate deny all
request_header_access Link deny all
request_header_access Cache-Control deny all
request_header_access Proxy-Connection deny all
request_header_access X-Cache deny all
request_header_access X-Cache-Lookup deny all
request_header_access Via deny all
request_header_access X-Forwarded-For deny all
request_header_access Pragma deny all
request_header_access Keep-Alive deny all
```

```
request_header_access Allow allow all
request_header_access Authorization allow all
request_header_access WWW-Authenticate allow all
request_header_access Proxy-Authorization allow all
request_header_access Proxy-Authenticate allow all
request_header_access Cache-Control allow all
request_header_access Content-Encoding allow all
request_header_access Content-Length allow all
request_header_access Content-Type allow all
request_header_access Date allow all
request_header_access Expires allow all
request_header_access Host allow all
request_header_access If-Modified-Since allow all
request_header_access Last-Modified allow all
request_header_access Location allow all
request_header_access Pragma allow all
request_header_access Accept allow all
request_header_access Accept-Charset allow all
request_header_access Accept-Encoding allow all
request_header_access Accept-Language allow all
request_header_access Content-Language allow all
request_header_access Mime-Version allow all
request_header_access Retry-After allow all
request_header_access Title allow all
request_header_access Connection allow all
request_header_access Proxy-Connection allow all
request_header_access User-Agent allow all
request_header_access Cookie allow all
request_header_access All deny all
```

```bash
sudo systemctl restart squid
```



## Logs de Acceso

Consultar los archivos de logs cuando ocurra a algún error, saber qué sitios web están siendo visitados por personas que se conecten al proxy.

```bash
sudo cat /var/log/squid/access.log
```

```bash
sudo cat /var/log/squid/cache.log
```



## Configuración para establecer conexión manual al proxy

![](https://i.imgur.com/BOgY4l9.png)

![](https://i.imgur.com/sE40GRH.png)



## Fuentes

> [Kacharreando](http://www.kacharreando.com/ubuntu/configurar-proxy-squid/)
>
> [Kifarunix](https://kifarunix.com/how-to-install-and-configure-squid-proxy-on-fedora-29-fedora-28-centos-7/2/)
>
> [Likegeeks](https://likegeeks.com/es/servidor-proxy-de-linux-squid/)
>
> [Eltallerdelbit](https://eltallerdelbit.com/squid-proxy/)
>
> [ClearOs](https://www.clearos.com/resources/documentation/clearos/content:en_us:kb_o_optimizing_performance_for_proxy_and_content_filter#)

