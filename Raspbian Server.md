# Instalación &&  Configuración de Raspbian Lite



## Software necesario 

[**SD Formatter**](https://www.sdcard.org/downloads/formatter/)

[**Raspbian Lite**](https://www.raspberrypi.org/downloads/raspbian/)

[**Etcher**](https://www.balena.io/etcher/)



## **Configurando el primer inicio**

**Habilitar SSH**

Crear archivo ***ssh*** en la carpeta boot para habilitar el servicio

**Habilitar && Configurar Red Inalambrica** 

Añadimos lo siguiente al final del archivo en la carpeta raíz **rooft**

```bash
sudo vim /etc/wpa_supplicant/wpa_supplicant.conf
```

> ```
> network={
>         ssid="nombre-de-wifi"
>         psk="password-de-wifi"
>         key_mgmt=WPA-PSK
> }
> ```



## Actualización && Configuración del  Sistema

Configuración basica del sistema

```bash
sudo raspi-config
```

Actualiza el Sistema completo

```bash
sudo rpi-update
```
Actualiza los paquetes

```bash
sudo apt update && sudo apt upgrade 
```
Instalar paquetes indispensables

```bash
sudo apt install vim nmap htop
```



## Instalación de Lamp

## Apache2

```bash
sudo apt install apache2
```

Cambia el propietario de las carpetas

```bash
sudo chown -R $user:www-data /var/www/html/
```

ó

```bash
sudo chown -R www-data:www-data /var/www/html
```

Cambia los permisos de acceso para las carpetas y ficheros 

```bash
sudo chmod -R 770 /var/www/html/
```

```bash
usermod -a -G www-data $user
```

##### Configuración del sitio web

```bash
sudo cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/$site.$com.conf 
```

```bash
sudo vim /etc/apache2/sites-available/$site.$com.conf
```

> ```
> <VirtualHost *:80>
>     ServerAdmin admin@ejemplo.com
>     ServerName ejemplo.com
>     ServerAlias www.ejemplo.com
>     DocumentRoot /var/www/ejemplo.com/html
>     ErrorLog ${APACHE_LOG_DIR}/error.log
>     CustomLog ${APACHE_LOG_DIR}/access.log combined
> </VirtualHost>
> ```

Habilitamos el archivo 

```bash
sudo a2ensite $site.$com.conf
```

Deshabilita el sitio por defecto

```bash
sudo a2dissite 000-default.conf
```

Probamos la configuración en busca de errores:

```bash
sudo apache2ctl configtest
```

Debería de mostrar la siguiente salida:

> ```
> Syntax OK
> ```

Reiniciar **Apache**

```bash
sudo systemctl restart apache2
```



## PHP

```bash
sudo install php libapache2-mod-php -y
```



## MySQL

```bash
sudo apt install mariadb-server mariadb-client php-mysql
```

```bash
sudo mysql_secure_installation
```

```bash
sudo mysql -uroot -p
```

##### Conexión Remota

```bash
sudo cp /etc/mysql/mariadb.conf.d/50-server.cnf /etc/mysql/mariadb.conf.d/50-server.cnf.backup
```

```bash
sudo vim /etc/mysql/mariadb.conf.d/50-server.cnf
```
> ```
> skip-external-locking
> ```
>
> ```
> bind-addres = 0.0.0.0
> ```

```bash
sudo systemctl restart mysql
```

```bash
sudo systemctl restart mariadb
```

```bash
mysql -u root -h localhost -p
```

Establecer Contraseña para acceder a MySQL

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED BY 'nueva-o-la-misma-contraseña';
```

> ```mysql
> CREATE USER '$user'@'%' IDENTIFIED BY '$password';
> ```
>
> ```mysql
> CREATE USER $USER@'192.168.1.66' IDENTIFIED BY $PASSW;
> ```
>
> ```mysql
> GRANT ALL PRIVILEGES ON $BASEDEDATOS.* TO '$user'@'192.168.1.*' IDENTIFIED BY '$password' WITH GRANT OPTION;
> ```
>
> ```mysql
> GRANT ALL PRIVILEGES ON $BASEDEDATOS.* TO '$user'@'%' IDENTIFIED BY '$password' WITH GRANT OPTION;
> ```
>
> ```mysql
> FLUSH PRIVILEGES;
> ```



## Servidor FTP

Creando un usuario para FTP

```bash
sudo adduser $user
```

```bash
sudo useradd -s /usr/sbin/nologin $user
```

Establecer contraseña

```bash
sudo passwd $user
```

```bash
sudo mkdir /home/$user/ftp
```

```bash
sudo chown -R nobody:nogroup /home/$user/ftp
```

Elimina los permisos de escritura

```bash
sudo chmod a-w /home/$user/ftp
```

```bash
sudo mkdir /home/$user/ftp/files
```

```bash
sudo chown $user:$user /home/$user2/ftp/files
```



### Instalación de vsftpd

```bash
sudo apt install vsftpd
```

Configuración del servidor FTP

```bash
sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.backup
```

```bash
sudo vim /etc/vsftpd.conf
```

> ```
> # Iniciar servidor al inicio del sistema.
> listen=NO
> listen_ipv6=YES
> # Podemos habilitar o deshabilitar el inicio de sesión de usuarios anónimos.
> anonymous_enable=YES
> # Para poder loguearnos usuarios locales (nosotros).
> local_enable=YES
> # Permiso de escritura en los directorios.
> write_enable=YES
> local_umask=022
> # Log de lo que hace el cliente en el servidor.
> dirmessage_enable=YES
> #Hora del servidor (por defecto).
> use_localtime=YES
> # Activate logging of uploads/downloads.
> xferlog_enable=YES
> # Puerto de escucha FTP, lo dejamos por defecto (el 20).
> connect_from_port_20=YES
> # Mensaje de entrada al servidor
> ftpd_banner=Bienvenido al servidor FTP de Bron
> # Podemos restringir a los usuarios a su propio /home/. Selecciona YES para restringir, y NO para tener acceso a todos los archivos y carpetas.
> chroot_local_user=YES
> #chroot_list_enable=YES
> #chroot_list_file=/etc/vsftpd.chroot_list
> 
> user_sub_token=$user
> local_root=/home/$user/ftp
> 
> secure_chroot_dir=/var/run/vsftpd/empty
> pam_service_name=vsftpd
> rsa_cert_file=/etc/ssl/private/vsftpd.pem
> ```

Añadir archivo 

```bash
sudo vim /etc/vsftpd.chroot_list
```

> ```
> $user
> ```

Reiniciar Servicio FTP

```bash
sudo systemctl restart vsftpd
```

**Conexión Anónima**

Acceso a la carpeta `/srv/ftp` que será compartida para todos los accesos anónimos.



## Java

Descargar desde la pagina original [Oracle](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

```bash
wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/8u131-b11/d54c1d3a095b4ff2b6607d096fa80163/jdk-8u131-linux-arm32-vfp-hflt.tar.gz
```

Crear carpeta 

```bash
sudo mkdir /usr/java
```

```bash
cd /usr/java
```

```bash
sudo mv /home/Downloads/jdk-8u131-linux-arm32-vfp-hflt.tar.gz /usr/java
```

```bash
sudo tar xf /usr/java/jdk-8u131-linux-arm32-vfp-hflt.tar.gz
```

```bash
sudo update-alternatives --install /usr/bin/java java /usr/java/jdk1.8.0_131/bin/java 1000
```

```bash
sudo update-alternatives --install /usr/bin/javac javac /usr/java/jdk1.8.0_131/bin/javac 1000
```



## Montar unidades NTFS en Raspbian

##### Instalar el paquete ntfs-3g

```bash
sudo apt install ntfs-3g
```

##### Saber  UIID 

```bash
sudo blkid
```

##### Añadir en /etc/fstab

```bash
PARTUUID=$UIDD  /mnt/MasterMedia ntfs-3g nofail,user,uid=$user,gid=$user 0 0
```

