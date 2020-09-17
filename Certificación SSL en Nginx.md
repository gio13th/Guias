# Certificación SSL en Nginx

## ¿Qué es un Certificado SSL?

Un ***certificado SSL*** (Secure Sockets Layer) es un título digital que autentifica la identidad de un sitio web y cifra con tecnología SSL la información que se envía al servidor. El cifrado es el proceso de **mezclar datos** en un formato indescifrable que solo puede volver al formato legible con la clave de descifrado adecuada.

Un certificado sirve como un "pasaporte" electrónico que establece las credenciales de una entidad en línea al hacer negocios en la Web. Cuando un usuario de Internet intenta enviar información de credenciales a un servidor web, el navegador del usuario accede al certificado digital del servidor y establece una conexión segura.

![](https://letsencrypt.org/images/howitworks_authorization.png)



## Instalar Certbot

El primer paso para obtener un certificado SSL mediante Let’s Encrypt, consiste en instalar el software de Certbot en tu servidor.

```bash
sudo apt-get install python-certbot-nginx certbot
```



## Permitir HTTPS a través del cortafuegos

Si se tiene el cortafuegos **ufw** habilitado, ajustar la configuración para permitir el tráfico HTTPS. 

Para permitir adicionalmente, el tráfico HTTPS, debes habilitar el perfil completo de Nginx y borrar los perfiles redundantes.

```bash
sudo ufw allow 'Nginx Full'
```

```bash
sudo ufw delete allow 'Nginx HTTP'
```



## Obtener el certificado SSL

Certbot provee diferentes maneras para obtener certificados SSL. Al usar el conector a Nginx, éste se encargará de reconfigurarlo, así como también, de recargar la configuración en caso de ser necesario.

```bash
sudo certbot --nginx -d example.com -d www.example.com
```



## Verificar la renovación automática de Certbot

Los certificados de Let’s Encrypt son válidos por *noventa días* únicamente. Esto se hace con el fin de incentivar a los usuarios para que automaticen el proceso de renovación de certificados. El paquete `certbot` que instalamos ya se ha ocupado de adicionar un archivo de comandos de renovación en `/etc/cron.d`. Este archivo de comandos se ejecuta dos veces al día y renovará automáticamente los certificados que presenten una fecha de expiración dentro de los siguientes treinta días.

```bash
sudo certbot renew --dry-run
```



## Habilitar puerto a través de PortForwarding 

![](https://i.imgur.com/pdOGfM0.png)



## Fuentes

> [Atareao](https://www.atareao.es/tutorial/nube-en-la-raspberry-pi/acceder-a-tu-raspberry-de-forma-segura/)
>
> [DigitalOcean](https://www.digitalocean.com/community/tutorials/como-asegurar-nginx-con-let-s-encrypt-en-ubuntu-18-04-es)
>
> [Godaddy](https://mx.godaddy.com/help/que-es-un-certificado-ssl-542)
>
> [Letsencrypt](https://letsencrypt.org/es/how-it-works/)
>
> [Openwebinars](https://openwebinars.net/blog/configurar-certificados-ssl-gratis-en-ubuntu-con-apache/)
>
> [Codigofacilito](https://codigofacilito.com/articulos/ssl-ubuntu-nginx)

