# Guía de Instalacíon de Fedora 30

### Actualización de paquetes 

```bash
sudo dnf update && sudo dnf upgrade
```



### Instalación de RPM Fusion

```bash
sudo dnf -y install --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-$(rpm -E %fedora).noarch.rpm http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-$(rpm -E %fedora).noarch.rpm
```



### Herramientas de Básicas de Compilación

```bash
sudo dnf -y groupinstall "Development Tools"  
```

```bash
sudo dnf -y groupinstall "Development Libraries" 
```



### Instalación de Códecs indispensables

```bash
sudo dnf -y install gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-libav gstreamer1-plugins-bad-free gstreamer1-plugins-bad-freeworld gstreamer1-plugins-bad-free-extras gstreamer1-plugins-good-extras gstreamer1-plugins-ugly gstreamer1-plugins-ugly-free
```



### Unrar && Zip

```bash
sudo dnf -y install unrar p7zip p7zip-plugins
```



### Limpiar Sistema

```bash
sudo dnf clean all
```



### Lenguajes de Programación 

##### Java

```bash
wget -c -O jre-oraclejava.rpm http://javadl.oracle.com/webapps/download/AutoDL?BundleId=220304_d54c1d3a095b4ff2b6607d096fa80163 
```

```bash
sudo dnf -y install jre-oraclejava.rpm 
```



##### NodeJS

```bash
sudo dnf -y install nodejs
```



##### MySQL

```bash
sudo dnf install -y https://dev.mysql.com/get/https://dev.mysql.com/get/mysql80-community-release-fc29-2.noarch.rpm
```

```bash
sudo dnf install mysql-community-server
```

```bash
sudo systemctl start mysqld.service
sudo systemctl enable mysqld.service
```

```bash
grep 'A temporary password is generated for root@localhost' /var/log/mysqld.log |tail -1
```

```bash
sudo mysql_secure_installation
```

```bash
mysql -h localhost -u root -p
```

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'nueva-o-la-misma-contraseña';
```



### Mi software personal

##### Snap

```bash
sudo dnf install snapd
```

```bash
sudo ln -s /var/lib/snapd/snap /snap/
```



##### Google Chrome

[Chrome](https://www.google.com/intl/es-419/chrome/ )



##### Gnome Tweak y otras herramientas

```bash
sudo dnf -y install gnome-tweak-tool deluge lollypop gimp inkscape vim htop wget vlc mpv gnome-mpv dconf-editor spotify-client tilix filezilla putty gthumb lm-sensors
```



##### Typora

[Typora](https://github.com/RPM-Outpost/typora)

```bash
./create-package.sh x64
```
