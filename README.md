Docker Primero Pasos
=========================

### Objetivos

* Instalar docker
* Comandos Basicos
* Trabajando con imagenes y contenedores
* Trabajando con Redes y Volumenes
* Multibuild 

### Requerimientos

* VM con Linux o Instancia Cloud.
* Conocimientos basicos de linux 

**El presente laboratorio se realizará con un OS Linux Centos 7.x**


Instalación
==================

> curl -fsSL https://get.docker.com -o get-docker.sh

> sh get-docker.sh

Este proceso de instalación se puede realizar en : Centos, Ubuntu

Debemos ahora deshabilitar SELinux : 

> vim /etc/selinux/config 

Cambiamos:  **Enforcing x Disabled

Ahora vamos a inicializar el servicio: 

> systemctl start docker

Imagenes y Contenedores
============================




