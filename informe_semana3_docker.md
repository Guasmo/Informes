# Práctica Semana 3: Contenerización con Docker – MySQL y phpMyAdmin

## 1. Título
Despliegue de contenedores Docker para MySQL y phpMyAdmin con red personalizada

---

## 2. Tiempo de duración
Aproximadamente 45 minutos

---

## 3. Fundamentos

Docker es una plataforma de virtualización a nivel de sistema operativo que permite empaquetar aplicaciones junto con todas sus dependencias en unidades portátiles llamadas **contenedores**. A diferencia de las máquinas virtuales tradicionales, los contenedores comparten el kernel del sistema operativo anfitrión, lo que los hace significativamente más ligeros y eficientes en el uso de recursos.

Un **contenedor Docker** es una instancia en ejecución de una imagen Docker. Una **imagen** es una plantilla de solo lectura que contiene el sistema de archivos, librerías, binarios y configuraciones necesarias para ejecutar una aplicación. Las imágenes se construyen en capas superpuestas, lo que permite reutilizar capas comunes entre diferentes imágenes, optimizando el almacenamiento y la transferencia de datos.

El **Docker Engine** es el motor principal que gestiona la creación, ejecución y eliminación de contenedores. Se comunica con el sistema operativo mediante llamadas al kernel para implementar el aislamiento usando tecnologías como **namespaces** (aislamiento de procesos, red, sistema de archivos) y **cgroups** (control de recursos como CPU y memoria).

Las **redes en Docker** son un componente fundamental para la comunicación entre contenedores. Docker ofrece varios tipos de redes: la red `bridge` (por defecto), que crea una red privada interna donde los contenedores pueden comunicarse; la red `host`, que elimina el aislamiento de red entre el contenedor y el host; y la red `none`, que deshabilita toda conectividad. Las **redes bridge personalizadas** son especialmente importantes porque habilitan la **resolución de nombres DNS automática**, permitiendo que los contenedores se comuniquen entre sí usando sus nombres como hostnames en lugar de direcciones IP, lo cual es una práctica recomendada en arquitecturas de microservicios.

Los **volúmenes Docker** resuelven el problema de la persistencia de datos. Por defecto, los datos generados dentro de un contenedor se pierden cuando este se elimina. Los volúmenes son directorios gestionados por Docker que existen fuera del ciclo de vida del contenedor, garantizando que los datos persistan incluso si el contenedor es destruido y recreado.

**MySQL** es un sistema gestor de bases de datos relacional de código abierto ampliamente utilizado en aplicaciones web. Organiza los datos en tablas con filas y columnas, y utiliza SQL (Structured Query Language) como lenguaje para consultas y manipulación de datos. En el contexto de Docker, la imagen oficial de MySQL permite configurar credenciales, bases de datos iniciales y usuarios mediante variables de entorno en el momento de creación del contenedor.

**phpMyAdmin** es una aplicación web desarrollada en PHP que proporciona una interfaz gráfica para administrar servidores MySQL y MariaDB. Permite realizar operaciones como crear y eliminar bases de datos, gestionar tablas, ejecutar consultas SQL, importar y exportar datos, y administrar usuarios y permisos, todo desde un navegador web sin necesidad de usar la línea de comandos. En un entorno Docker, phpMyAdmin se configura para conectarse a un contenedor MySQL mediante la variable de entorno `PMA_HOST`, que acepta el nombre del contenedor MySQL como hostname gracias al DNS interno de la red personalizada.

La arquitectura de esta práctica sigue el principio de **separación de responsabilidades**: cada servicio (base de datos y administración web) corre en su propio contenedor aislado, se comunican a través de una red privada definida por software, y los datos críticos se persisten mediante volúmenes independientes del ciclo de vida de los contenedores.

---

## 4. Conocimientos previos

Para realizar esta práctica el estudiante necesita tener claro los siguientes temas:

- Comandos básicos de Linux (navegación, permisos, terminal).
- Conceptos de redes: IP, puertos, protocolos TCP/IP.
- Fundamentos de bases de datos relacionales y SQL básico.
- Instalación y uso básico de Docker Engine.
- Manejo de navegador web.
- Concepto de variables de entorno en sistemas operativos.

---

## 5. Objetivos a alcanzar

- Crear una red personalizada de tipo bridge en Docker para permitir comunicación entre contenedores.
- Desplegar un contenedor con MySQL 8.0 configurando credenciales y persistencia de datos mediante volúmenes.
- Desplegar un contenedor con phpMyAdmin conectado al contenedor MySQL a través de la red personalizada.
- Verificar la conectividad entre ambos contenedores mediante inspección de la red.
- Crear una base de datos y tabla de prueba desde la interfaz gráfica de phpMyAdmin.

---

## 6. Equipo necesario

- Computador con sistema operativo Linux (Arch Linux en esta práctica).
- Docker Engine v24.0 o superior instalado y en ejecución.
- Acceso a internet para descarga de imágenes desde Docker Hub.
- Navegador web (Firefox, Chrome, etc.).
- Terminal de comandos.

---

## 7. Material de apoyo

- Documentación oficial de Docker: https://docs.docker.com
- Imagen oficial de MySQL en Docker Hub: https://hub.docker.com/_/mysql
- Imagen oficial de phpMyAdmin en Docker Hub: https://hub.docker.com/_/phpmyadmin
- Documentación de redes en Docker: https://docs.docker.com/network/
- Guía de la asignatura semana 3.
- Cheat sheet de comandos Docker.

---

## 8. Procedimiento

**Paso 1: Verificar que Docker Engine está en ejecución**

Antes de comenzar, se verifica que el servicio de Docker esté activo en el sistema con el comando `docker --version` y `docker ps`. Si el daemon no está corriendo, se inicia con `sudo systemctl start docker`.

**Paso 2: Crear la red personalizada de tipo bridge**

Se crea una red Docker personalizada llamada `red-mysql-php` que permitirá la comunicación entre los contenedores por nombre de host gracias al DNS interno de Docker:

```bash
docker network create red-mysql-php
```

Se verifica la creación con:

```bash
docker network ls
```

La salida muestra la red `red-mysql-php` con driver `bridge` y scope `local`.

**Paso 3: Desplegar el contenedor MySQL**

Se crea y ejecuta el contenedor de MySQL 8.0 con las credenciales necesarias, conectado a la red personalizada y con un volumen para persistencia de datos:

```bash
docker run -d \
  --name mysql-container \
  --network red-mysql-php \
  -e MYSQL_ROOT_PASSWORD=rootpass123 \
  -e MYSQL_DATABASE=mi_base_datos \
  -e MYSQL_USER=usuario_app \
  -e MYSQL_PASSWORD=userpass123 \
  -v mysql_data:/var/lib/mysql \
  mysql:8.0
```

Las variables de entorno configuran: la contraseña del usuario root (`MYSQL_ROOT_PASSWORD`), una base de datos inicial (`MYSQL_DATABASE`), y un usuario adicional con su contraseña (`MYSQL_USER` y `MYSQL_PASSWORD`). El volumen `mysql_data` garantiza que los datos persistan aunque el contenedor sea eliminado.

**Paso 4: Desplegar el contenedor phpMyAdmin**

Se crea y ejecuta el contenedor de phpMyAdmin, conectándolo a la misma red y apuntándolo al contenedor MySQL mediante su nombre:

```bash
docker run -d \
  --name phpmyadmin-container \
  --network red-mysql-php \
  -e PMA_HOST=mysql-container \
  -e PMA_PORT=3306 \
  -p 8080:80 \
  phpmyadmin:latest
```

La variable `PMA_HOST=mysql-container` usa el nombre del contenedor MySQL como hostname, posible gracias al DNS interno de la red `red-mysql-php`. El mapeo `-p 8080:80` expone el puerto 80 del contenedor en el puerto 8080 del host.

**Paso 5: Verificar conectividad entre contenedores**

Se inspecciona la red para confirmar que ambos contenedores están correctamente conectados:

```bash
docker network inspect red-mysql-php
```

La salida confirma que tanto `mysql-container` (IP: 172.19.0.2) como `phpmyadmin-container` (IP: 172.19.0.3) están registrados en la red `red-mysql-php`, compartiendo la subred `172.19.0.0/16`.

**Paso 6: Acceder a phpMyAdmin**

Se abre el navegador y se accede a `http://localhost:8080`. Se inicia sesión con las credenciales del usuario root:

- **Usuario:** `root`
- **Contraseña:** `rootpass123`

**Paso 7: Crear base de datos de prueba**

Desde el panel de phpMyAdmin se selecciona "Nueva" en el panel izquierdo, se escribe el nombre `base_prueba_semana3`, se selecciona el cotejamiento `utf8mb4_general_ci` y se hace clic en "Crear".

**Paso 8: Crear tabla de prueba**

Dentro de la base de datos `base_prueba_semana3` se crea la tabla `estudiantes` con las siguientes columnas:

| Columna | Tipo | Longitud | Atributo |
|---------|------|----------|----------|
| `id` | INT | — | AUTO_INCREMENT, PRIMARY KEY |
| `nombre` | VARCHAR | 100 | — |
| `email` | VARCHAR | 150 | — |
| `fecha_registro` | DATE | — | — |

Se guarda la tabla y se verifica su estructura desde la pestaña "Estructura" de phpMyAdmin.

---

## 9. Resultados esperados

Al finalizar la práctica se obtienen los siguientes resultados:

- Dos contenedores Docker en ejecución: `mysql-container` y `phpmyadmin-container`, visibles con `docker ps` en estado `Up`.
- Una red personalizada `red-mysql-php` de tipo bridge con ambos contenedores registrados, confirmado mediante `docker network inspect red-mysql-php`.
- Acceso funcional a la interfaz web de phpMyAdmin en `http://localhost:8080`, autenticado con las credenciales configuradas.
- Base de datos `base_prueba_semana3` creada exitosamente y visible en el panel lateral de phpMyAdmin.
- Tabla `estudiantes` creada con su estructura de cuatro columnas correctamente definidas.
- Comunicación verificada entre phpMyAdmin y MySQL a través de la red Docker personalizada, demostrando el funcionamiento del DNS interno entre contenedores.

---

## 10. Bibliografía

- Docker Inc. (2024). *Docker Documentation - Networking overview*. https://docs.docker.com/network/
- Docker Inc. (2024). *Docker Hub - MySQL official image*. https://hub.docker.com/_/mysql
- Docker Inc. (2024). *Docker Hub - phpMyAdmin official image*. https://hub.docker.com/_/phpmyadmin
- Turnbull, J. (2014). *The Docker Book: Containerization is the new virtualization*. James Turnbull.
- MySQL AB. (2024). *MySQL 8.0 Reference Manual*. Oracle Corporation. https://dev.mysql.com/doc/refman/8.0/en/
