## Docker Primero Pasos


#### Objetivos

* Instalar docker
* Comandos Basicos
* Trabajando con imagenes y contenedores
* Trabajando con Redes y Volumenes
* Multibuild 

#### Requerimientos

* VM con Linux o Instancia Cloud.
* Conocimientos basicos de linux 

**El presente laboratorio se realizará con un OS Linux Centos 7.x**


## Instalación


> curl -fsSL https://get.docker.com -o get-docker.sh

> sh get-docker.sh

Este proceso de instalación se puede realizar en : Centos, Ubuntu

Debemos ahora deshabilitar SELinux : 

> vim /etc/selinux/config 

Cambiamos:  **Enforcing x Disabled

Ahora vamos a inicializar el servicio: 

> systemctl start docker


## Imágenes y Contenedores

Recordemos que las imagenes son "instancias" de aplicaciones y/o sistemas operativos, vamos a trabajar con los sgts comandos:

Buscar una imagen:

> docker search nginx

Instalar una imagen:

> docker pull nginx 

Listar imagenes:

> docker images ls | docker image ls 

Borrar una imagen:

> docker rmi ID_IMAGEN | docker rmi nginx 

Bien, con estos comandos, vamos ahora a iniciar un contenedor: 

> docker run -it docker/doodle:birthday 

Y que pasa si lo ejecutamos asi:

> docker run -it --rm docker/doodle:birthday

El comando *run* nos permite crear contenedores, a diferencia del comando *start*, este se aplica cuando el contenedor ya se encuetra creado.

Con contenedores trabajaremos estos comandos:

Crear un contenedor:

> docker run nginx

Listar contenedores:

> docker ps | docker ps -a | docker container ps 

Iniciar un contenedor:

> docker start ID_CONTAINER 

Detener un contenedor:

> docker stop ID_CONTAINER

Borrar un contenedor:

> docker rm ID_CONTAINER | docker rm -fv NAME_CONTAINER 

OBS.

Un comando importante es *inspect*. este comando nos ayuda  a revisar informacion particular que necesitamos de un contenedor, imagen, red o volumen.

> docker inspect ID_CONTAINER

Hasta el momento, conocemos los comandos basicos para trabajar con contenedores e imagenes, ahora agregemos un poco de redes y volumenes:

En docker tenemos 3 tipos de red: 

* Bridge
* Host
* None

Para listar las redes que tenemos usamos:

> docker network ls 

Y para crear una red usamos:

> docker network create my_net

Tambien podemos hacerlo de esta forma:

> docker network create --driver bridge my_net2 

De forma similar tenemos 3 tipos de volumenes:

* Volume
* Bind Volume
* tmpfs

Veamos algunos ejemplos:

Creamos un volume

> docker volume create VG_DATA 

Listamos volumenes existenteS:

> docker volume ls 

Creamos un contenedor con un bind volumen 

> docker run -dit --name bbva -v /var/lib/mysql2:/var/lib/mysql ubuntu

Y ahora uno del tipo tmpfs:

> docker run -dit --name bbvape --tmpfs /var/html/tempo ubuntu


Multibuild
===========

Multistage/Multibuild nos va a permitir construir imagenes sobre imagenes, esto quiere decir, que podemos usar una imagen base para construir un artefacto y luego usar otra imagen para copiar el artefacto creado, y todo esto bajo un mismo Dockerfile, vamos a crear una carpeta de nombre multibuild y dentro de ella crearemos los ficheros main.go y Dockerfile, asumamos que tenemos una pequeña aplicación desarrollada en Go! 

```
package main
import (
    "fmt"
    "net/http")

func main() {
    http.HandleFunc("/", func(w http.ResponseWriter, r *http.Request) {
        fmt.Fprintf(w, "Hola Multibuild Docker: %s\n", r.URL.Path)
    })

    http.ListenAndServe("0.0.0.0:8080", nil)
}
```

Guardamos este código como **main.go**, y ahora vamos a crear el siguiente Dockerfile:
``` 
FROM golang:1.8
WORKDIR  /go/src/app 
COPY main.go     .
RUN go build -o webserver   .
CMD ["./webserver"]
```

Y procedemos a construir la imagen:

> docker build -t imggo . 

Ahora listamos la imagen creada para ver su tamaño:
```
docker images | grep imggo
imggo        latest              7dde5fd28e3b        54 seconds ago       719MB
``` 
El tamaño de nuestra imagen creada es de **719 MB.**

Ahora, vamos a hacer una pequeña modificación, vamos a cambiar esta linea en Dockerfile
``` 
FROM golang:alpine
``` 

Y generemos una nueva imagen:

> docker build -t imggo1  .

Listamos ahora el tamaño de la imagen:
```
docker images | grep imggo1
imggo1  latest    a7949d966f2b        7 seconds ago       367MB
``` 

Esto se debe a que alpine es una imagen base más compacta, pero se podrá reducir aún más?, vamos ahora a realizar el proceso de multibuild.  Vamos ahora a usar este 
Dockerfile .
```
FROM golang:alpine AS builder
WORKDIR  /go/src/app 
COPY main.go .
RUN go build -o webserver .
FROM alpine 
WORKDIR /app
COPY --from=builder /go/src/app/  /app/
CMD ["./webserver"]
``` 

y lo construimos:

> docker build   -t imggo2  .
 
Listamos ahora:
```
docker images | grep imggo
imggo2         latest              59dbf19a203a        11 seconds ago      13MB
```

De esta forma multibuild nos ayuda a reducir el tamaño de nuestras imágenes y hacerlas más versátiles.








