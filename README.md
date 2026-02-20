# Práctica: Despliegue de WordPress con HTTPS mediante Docker Compose

**Asignatura:** Implantación de Aplicaciones Web (IAW)  
**Curso:** 2024/2025  
**Alumna:** Marina  
**URL del sitio:** [https://practicamarinahttps.ddns.net](https://practicamarinahttps.ddns.net)

---

## 1. Introducción
Esta práctica tiene como objetivo la puesta en producción de un CMS **WordPress** utilizando una arquitectura de microservicios orquestada con **Docker Compose**. La principal mejora respecto a despliegues básicos es la implementación de seguridad mediante el protocolo **HTTPS**, utilizando certificados SSL gratuitos emitidos por la Autoridad de Certificación **Let's Encrypt**.

Para lograr la automatización del certificado, se utiliza la imagen **HTTPS-PORTAL**, que funciona como un servidor Nginx automatizado que implementa el protocolo **ACME** para la validación, obtención y renovación automática de los certificados de seguridad.

---

## 2. Descripción del entorno y Requisitos
Para garantizar que la aplicación WordPress funcione de manera fluida (dado que requiere ciertos recursos de CPU y RAM), se han cumplido los siguientes requisitos técnicos:

* **Infraestructura**: Se utiliza una instancia **EC2 de AWS** con una imagen de Ubuntu 24.04 LTS.
* **Capacidad**: Se han asignado **20 GB de almacenamiento** para permitir que los volúmenes de la base de datos y los archivos multimedia crezcan sin problemas de espacio.
* **Configuración de Red**: En el Security Group de AWS se han habilitado tres reglas fundamentales:
    * **Puerto 22 (SSH)**: Para la gestión de archivos y comandos mediante terminal.
    * **Puerto 80 (HTTP)**: Necesario para que Let's Encrypt realice el desafío de validación.
    * **Puerto 443 (HTTPS)**: Para servir la web de forma segura al usuario final.

---

## 3. Acciones realizadas paso a paso

### 3.1. Preparación y Gestión del Dominio
Dado que el servicio Freenom se encuentra fuera de servicio, se ha optado por la siguiente solución para la resolución de nombres:
1.  **Registro en No-IP**: Se ha mantenido el nombre de dominio `practicamarinahttps.ddns.net`.
2.  **Configuración de Registros A**: Se ha vinculado el dominio con la dirección **IP pública** de la instancia de AWS. Esto es crítico para que Let's Encrypt pueda verificar que somos los propietarios del servidor al que apunta el dominio.

### 3.2. Instalación de las Herramientas de Contenedores
En la terminal de la instancia, se procedió a preparar el motor de ejecución de contenedores:
```bash
sudo apt update
sudo apt install docker.io docker-compose -y
sudo systemctl start docker
sudo systemctl enable docker
