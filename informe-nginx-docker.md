# Práctica No. 1 - Servidores Web con Nginx usando Docker

## 1. Título

Creación y personalización de dos servidores web con Nginx usando Docker

## 2. Tiempo de duración

90 minutos aproximadamente

## 3. Fundamentos

**Docker** es una plataforma de contenedorización que permite empaquetar aplicaciones y sus dependencias en unidades llamadas contenedores. A diferencia de las máquinas virtuales, los contenedores comparten el kernel del sistema operativo anfitrión, lo que los hace mucho más ligeros y rápidos de iniciar. Docker utiliza imágenes como plantillas de solo lectura a partir de las cuales se crean los contenedores en ejecución.

Una **imagen Docker** es una plantilla inmutable que contiene todo lo necesario para ejecutar una aplicación: código, librerías, variables de entorno y archivos de configuración. Las imágenes se almacenan en registros como Docker Hub, desde donde pueden descargarse con el comando `docker pull` o automáticamente al usar `docker run`.

Un **contenedor Docker** es una instancia en ejecución de una imagen. Cada contenedor es aislado del resto del sistema, tiene su propio sistema de archivos, red y procesos. Sin embargo, puede comunicarse con el exterior mediante la exposición de puertos, lo que permite acceder a los servicios que corren dentro desde el navegador o cualquier cliente HTTP.

**Nginx** (pronunciado "engine-x") es un servidor web de alto rendimiento, ampliamente utilizado como servidor HTTP, proxy inverso y balanceador de carga. Es conocido por su bajo consumo de recursos y su capacidad para manejar un gran número de conexiones simultáneas. Nginx sirve archivos estáticos de manera muy eficiente y su archivo principal de contenido web es `index.html`, ubicado por defecto en `/usr/share/nginx/html/` dentro del contenedor.

La **exposición de puertos** en Docker se realiza con la opción `-p`, que mapea un puerto del sistema anfitrión a un puerto del contenedor. Por ejemplo, `-p 8089:80` significa que el puerto `8089` del equipo local redirige al puerto `80` del contenedor, que es donde Nginx escucha por defecto.

El comando `docker cp` permite copiar archivos entre el sistema anfitrión y un contenedor en ejecución, sin necesidad de acceder al contenedor por SSH. Esto es fundamental para editar archivos de configuración o contenido web de manera práctica.

Al desplegar múltiples contenedores Nginx con diferentes puertos, es posible servir sitios web distintos desde el mismo equipo, lo que es muy útil para entornos de desarrollo, pruebas o demostraciones de múltiples proyectos simultáneamente.

Este tipo de despliegue es una práctica común en arquitecturas modernas de microservicios, donde cada servicio corre en su propio contenedor de forma independiente y escalable.

## 4. Conocimientos previos

Para realizar esta práctica el estudiante necesita tener claro los siguientes temas:

- Uso básico de la terminal de Linux
- Comandos básicos de Linux (`ls`, `cd`, `cat`, `cp`)
- Concepto de servidor web y protocolo HTTP
- Uso de editores de texto en terminal (nano o nvim)
- Conceptos básicos de HTML
- Manejo del navegador web para verificar resultados

## 5. Objetivos a alcanzar

- Desplegar contenedores Docker a partir de una imagen oficial de Nginx
- Exponer puertos de contenedores para acceder a los servidores desde el navegador
- Copiar y editar archivos dentro de contenedores en ejecución con `docker cp`
- Personalizar el contenido HTML de cada servidor web
- Verificar el funcionamiento de dos servidores web corriendo simultáneamente en diferentes puertos

## 6. Equipo necesario

- Computador con sistema operativo Linux, Windows (WSL) o Mac
- Terminal de comandos
- Docker instalado (versión 20.x o superior)
- Editor de texto en terminal (nano o nvim)
- Navegador web (Chrome, Firefox, etc.)
- Conexión a internet (para descargar la imagen de Nginx)

## 7. Material de apoyo

- Documentación oficial de Docker: https://docs.docker.com
- Documentación oficial de Nginx: https://wiki.archlinux.org/title/Nginx
- Guía de la asignatura
- Cheat sheet de comandos Docker y Linux

## 8. Procedimiento

### Instalación y configuración de Docker

Antes de comenzar, se debe tener Docker instalado. En sistemas basados en Ubuntu/Debian se instala con los siguientes comandos:

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

Para verificar que Docker está correctamente instalado:

```bash
docker --version
```

En caso de necesitar ejecutar Docker sin `sudo`, agregar el usuario al grupo docker:

```bash
sudo usermod -aG docker $USER
```

Cerrar sesión y volver a entrar para que el cambio tome efecto.

---

### Paso 1: Verificar contenedores en ejecución

Antes de crear los contenedores, se verificó el estado actual de Docker:

```bash
docker ps
```

### Paso 2: Crear el primer contenedor Nginx (nginx1)

Se creó el primer contenedor llamado `nginx1`, exponiendo el puerto `8089` del sistema anfitrión al puerto `80` del contenedor:

```bash
docker run -d --name nginx1 -p 8089:80 nginx
```

- `-d`: ejecuta el contenedor en segundo plano (detached)
- `--name nginx1`: asigna el nombre nginx1 al contenedor
- `-p 8089:80`: mapea el puerto 8089 del host al puerto 80 del contenedor
- `nginx`: imagen oficial de Nginx en Docker Hub

### Paso 3: Crear el segundo contenedor Nginx (nginx2)

Se creó el segundo contenedor llamado `nginx2`, exponiendo el puerto `8090`:

```bash
docker run -d --name nginx2 -p 8090:80 nginx
```

### Paso 4: Verificar que ambos contenedores están activos

```bash
docker ps
```

Ambos contenedores deben aparecer en estado `Up`.

### Paso 5: Copiar el index.html de nginx1 al sistema anfitrión

Se extrajo el archivo `index.html` del contenedor `nginx1` hacia el directorio actual del sistema anfitrión:

```bash
docker cp nginx1:/usr/share/nginx/html/index.html ./index1.html
```

### Paso 6: Editar index1.html con información institucional

Se abrió el archivo con el editor `nvim` para personalizarlo con información del instituto:

```bash
nvim index1.html
```

Contenido del archivo editado:

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Instituto - Nginx1</title>
</head>
<body>
  <h1>Instituto Tecnológico</h1>
  <p>Informacion instituto tecnologico sudamericano</p>
  <p>Carrera: Desarrollo de software</p>
  <p>Materia: Tendencias Tecnológicas</p>
</body>
</html>
```

### Paso 7: Copiar el archivo editado de vuelta al contenedor nginx1

Una vez editado, se copió el archivo de regreso al contenedor:

```bash
docker cp index1.html nginx1:/usr/share/nginx/html/index.html
```

### Paso 8: Copiar el index.html de nginx2 al sistema anfitrión

Se repitió el proceso para el segundo contenedor:

```bash
docker cp nginx2:/usr/share/nginx/html/index.html ./index2.html
```

### Paso 9: Editar index2.html con información personal

```bash
nvim index2.html
```

Contenido del archivo editado:

```html
<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <title>Estudiante - Nginx2</title>
</head>
<body>
  <h1>Juan Buri</h1>
  <p>Estudiante de desarrollo de software</p>
  <p>20, cuenca - ecuador</p>
  <p>Desarrollador de software</p>
</body>
</html>
```

### Paso 10: Copiar el archivo editado de vuelta al contenedor nginx2

```bash
docker cp index2.html nginx2:/usr/share/nginx/html/index.html
```

### Paso 11: Verificar los servidores en el navegador

Se abrió el navegador y se accedió a las siguientes URLs:

- Servidor institucional (nginx1): `http://localhost:8089`
- Servidor personal (nginx2): `http://localhost:8090`

## 9. Resultados esperados

Al finalizar la práctica se obtuvieron los siguientes resultados:

- Dos contenedores Docker con Nginx corriendo simultáneamente en los puertos `8089` y `8090`
- El servidor `nginx1` en `http://localhost:8089` muestra correctamente la página con información institucional personalizada
- El servidor `nginx2` en `http://localhost:8090` muestra correctamente la página con información personal del estudiante
- Se comprobó el uso de `docker cp` para transferir archivos entre el sistema anfitrión y los contenedores sin necesidad de acceder por SSH
- Se verificó que la edición del `index.html` y su copia de regreso al contenedor actualiza el contenido del servidor web de forma inmediata

Como resultado general, se logró desplegar y personalizar dos servidores web independientes usando Docker y Nginx, comprendiendo el flujo completo de creación de contenedores, exposición de puertos y edición de archivos dentro de contenedores en ejecución.

## 10. Bibliografía
