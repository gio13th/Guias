# Docker

**Docker** es una plataforma de software que le permite crear, probar e implementar aplicaciones rápidamente. Docker empaqueta software en unidades estandarizadas llamadas [contenedores](https://aws.amazon.com/es/containers/) que incluyen todo lo necesario para que el software se ejecute, incluidas bibliotecas, herramientas de sistema, código y tiempo de ejecución. Con Docker, puede implementar y ajustar la escala de aplicaciones rápidamente en cualquier entorno con la certeza de saber que su código se ejecutará.

![https://miro.medium.com/max/980/1*2hBTIlLi-8kLD0vkvTFSVA.png](https://miro.medium.com/max/980/1*2hBTIlLi-8kLD0vkvTFSVA.png)



## Cómo funciona Docker

Docker le proporciona una manera estándar de ejecutar su código. Docker es un **sistema operativo** para contenedores. De manera similar a cómo una **máquina virtual** virtualiza (elimina la necesidad de administrar directamente) el hardware del servidor, *los contenedores virtualizan el sistema operativo de un servidor*. Docker se instala en cada servidor y proporciona comandos sencillos que puede utilizar para crear, iniciar o detener contenedores. 

> Una máquina virtual es un software que permite emular el funcionamiento de un ordenador dentro de otro ordenador.
>
> Una máquina virtual es un software que crea una capa independiente donde se emula el funcionamiento de un ordenador real con todos los componentes de hardware que necesita para funcionar (disco duro, memoria RAM, tarjetas de red, tarjeta gráfica, etc.) y que puede ejecutar cualquier sistema operativo o programa, tal y como lo haría un ordenador real. Toda esta emulación se encapsula en una serie de archivos que actúan como contenedor desde el que se ejecuta la máquina virtual en una ventana de tu ordenador como si de un programa más se tratara y sin que nada de lo que suceda en el interior de esa ventana afecte al ordenador que la ejecuta.

![monolith_2-VM-vs-Containers](https://d1.awsstatic.com/Developer%20Marketing/containers/monolith_2-VM-vs-Containers.78f841efba175556d82f64d1779eb8b725de398d.png)



## ¿Que es un contenedor?

Un contenedor es una pequeña instancia, que debe ser:

- Ejecutable
- Ligera
- Autónoma

Esta instancia tiene su propio sistema operativo y sus binarios/librerías, para ejecutar su aplicación.

![img](https://miro.medium.com/max/586/0*VaYQn55Tc3xy8-lN)

El objetivo es que cada contenedor se ejecute para actividades especificas. Cada contenedor funciona sobre la **“infraestructura”** de su equipo host, que puede tener otro Sistema Operativo, lo que hace a los contenedores **“independientes”** de la plataforma de su host.



## Instalación de Docker en Ubuntu

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

```bash
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

```bash
sudo apt update
```

```bash
sudo apt install docker-ce
```



```bash
sudo systemctl status docker
```



## Instalación de Docker en Raspbian

```bash
sudo apt-get install apt-transport-https ca-certificates software-properties-common -y
```

```bash
curl -fsSL get.docker.com -o get-docker.sh && sh get-docker.sh
```

```bash
sudo usermod -aG docker pi
```

```bash
sudo curl https://download.docker.com/linux/raspbian/gpg
```

```bash
vim /etc/apt/sources.list
```

```
deb https://download.docker.com/linux/raspbian/ stretch stable
```



## Instalar Docker Compose

```bash
sudo apt-get install libffi-dev libssl-dev
```

```bash
sudo apt install python3-dev
```

```bash
sudo apt install python3-pip
```

```bash
sudo pip3 -v install docker-compose
```



## Creación de Dockerfile

Docker construye “imágenes” al leer, *línea por línea*, las instrucciones desde un **“Dockerfile”**, que es un archivo de texto, que tiene todos los comandos en orden y que son necesarios para construir esta imagen. Este archivo debe cumplir cierto formato para declarar las instrucciones.

> *La imagen de Docker, consiste en la lectura de cada una de las capas que se presentan como instrucciones en un Dockerfile.*



### Archivo Dockerfile

```
#Contenedor v.1.0
FROM ubuntu:20.04
ARG DEBIAN_FRONTEND=noninteractive
RUN apt-get update -y && apt-get install apache2 -y
CMD /usr/sbin/apache2ctl -D FOREGROUND
ENTRYPOINT echo "Eres un maestro!!! esta es tu nueva imagen de Ubuntu con Apache ;)"
```

En la sintaxis del archivo de docker, las instrucciones van en **mayúsculas**. Cada instrucción crea una **“capa”**. 

- [ ] **FROM [imagen]:[version]** crea una capa desde un repositorio de imágenes llamado [DockerHUB](https://hub.docker.com/)*. En este caso estamos usando una imagen con* Ubuntu y su versión en especifico 18.04
- [ ] **RUN [comando de ejecución + parámetros]** ejecuta un comando durante el **proceso de creación del contenedor**, en este caso hace el update del sistema con “apt-get update” y luego concatena una segunda instrucción con **“&&”** y ejecuta **“apt-get install nginx -y”** para instalar el servidor web “nginx”
- [ ] **CMD** Es una instrucción para que un comando se ejecute **dentro del contenedor**, justo después de que el contenedor sea ejecutado.
- [ ] **ENTRYPOINT o CMD [comando]** Es una instrucción para que un comando se ejecute **dentro del contenedor**, justo después de que el contenedor sea ejecutado



### Construyendo el contenedor

Para la construcción de la imagen, debemos enviar el comando `docker build`, en el directorio donde tenemos nuestro Dockerfile. Este directorio se denomina **“build context”** (contexto de construcción).

```bash
docker build -t apache2:20.04 . 
```

- [ ] **[-t]** Nombre y opcionalmente colocamos un tag (etiqueta), bajo el formato ‘nombre:tag’
- [ ] **<nombre-de-imagen>:<tag>** en este ejemplo el nombre de la imagen que se asigna es: “apache2” y el tag es el versionamiento de esta imagen, donde puedes colocar cualquier cosa, en este ejemplo es *20.04*
- [ ] **[.]** Construye la imagen desde dentro del build context



### Ejecuta tu contenedor

```bash
docker run -ti -d --name apache2-svr -p 88:80 apache2:20.04
```

- [x] **-d** (detached) para que la terminal no quede capturada por la ejecución del contenedor. Si no se especifica -d la terminal queda inhabilitada hasta que se envié un comando de terminar como Ctrl-C.
- [ ] **-ti** (terminal interactiva) permite ver las respuesta de los comandos que se enviaron al contenedor. 
- [ ] **--name** para asignar nombre del contenedor, si no se asigna un nombre, docker coloca un nombre de manera aleatoria. 
- [ ] **-p** ahora nuestro host (nuestro equipo) estará escuchando en todos los interfaces *(0.0.0.0)* en e puerto **88**, y el tráfico será redirigido al puerto 80 dentro del contenedor.
- [ ] **<nombre-de-imagen>:<tag>** desde que imagen se ejecutará el contenedor.

>  *la opcion [-p] se puede utilizar múltiples veces. Esto es útil si el contenedor utiliza múltiples puertos.*



###  Conectarse al contenedor vía SSH

Para conectarse a un contenedor que esta activo podemos usar `**docker exec**` para utilizar un shell.

```bash
docker exec -it apache2-svr /bin/bash
```



## Funciones Extras

### Listar imágenes dockers

```bash
docker image -l
docker images
```

### Borrar imágenes

```bash
docker rmi <id_image1> <id_image2> <id_image 3>
```

### Borrar todas las imágenes

```bash
docker rmi $(docker images -a -q)
```

### Buscar contenedores

```bash
docker ps -a
```

### Borrar contenedores

```bash
docker rm <id_container> <id_container> <id_container 3>
```

### Borrar todos los contenedores

```bash
docker rm $(docker ps -a -q)
```



## Fuentes

[Amazon](https://aws.amazon.com/es/docker/)

[Medium](https://medium.com/@jclopex/que-es-docker-14f16ad314c9)

[RedHat](https://www.redhat.com/es/topics/containers/what-is-docker)

[Docker](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

[Vidaxp](https://www.vidaxp.com/tecnologia/como-borrar-imagenes-contenedores-y-volumenes-docker/)

[Hostinger](https://www.hostinger.mx/tutoriales/como-instalar-y-usar-docker-en-ubuntu/)

[Docker](https://www.docker.com/blog/happy-pi-day-docker-raspberry-pi/)

[Dev.to](https://dev.to/rohansawant/installing-docker-and-docker-compose-on-the-raspberry-pi-in-5-simple-steps-3mgl)

[DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-ubuntu-18-04)
