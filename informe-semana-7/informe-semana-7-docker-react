# Práctica No. 7 - Aplicación React con Docker

## 1. Título

Contenedorización y despliegue de una aplicación frontend React con su servicio backend simulado (Mock API) utilizando Dockerfile personalizados y Docker Compose

---

## 2. Tiempo de duración

45 minutos aproximadamente

---

## 3. Fundamentos

**Docker** es una plataforma de contenedorización que permite empaquetar aplicaciones junto con todas sus dependencias en unidades aisladas y reproducibles llamadas contenedores. A diferencia de las máquinas virtuales, los contenedores comparten el kernel del sistema operativo anfitrión, lo que los hace más ligeros y eficientes en el uso de recursos.

Un **Dockerfile** es un archivo de texto que contiene las instrucciones necesarias para construir una imagen Docker paso a paso. Cada instrucción genera una capa en la imagen final. Las instrucciones más comunes son: `FROM` (imagen base), `WORKDIR` (directorio de trabajo), `COPY` (copiar archivos al contenedor), `RUN` (ejecutar comandos durante el build), `EXPOSE` (declarar el puerto que usará el contenedor) y `CMD` (comando que se ejecuta al iniciar el contenedor).

**Vite** es una herramienta de build moderna para aplicaciones frontend. En modo producción, genera archivos estáticos optimizados mediante el comando `npm run build`. El comando `npm run preview` permite servir esos archivos compilados localmente. Para que Vite Preview sea accesible desde fuera del contenedor es necesario pasar los flags `--host 0.0.0.0 --port 4173`, de lo contrario el servidor solo escucha en la interfaz interna del contenedor.

**json-server** es una librería de Node.js que permite crear una API REST completa a partir de un archivo `db.json`, sin necesidad de escribir código de servidor. Es ideal para entornos de desarrollo y pruebas. En esta práctica el frontend consume datos desde `http://localhost:3100`, dirección configurada en el archivo `src/services/api/classmatesApi.ts`.

**Docker Compose** es una herramienta oficial de Docker que permite definir y ejecutar aplicaciones multi-contenedor mediante un archivo YAML (`compose.yaml` o `docker-compose.yml`). Con un único comando `docker compose up` se crean y levantan todos los servicios definidos, junto con sus redes y volúmenes asociados, eliminando la necesidad de ejecutar múltiples comandos `docker run` de forma individual.

La opción **`network_mode: host`** en Docker Compose hace que el contenedor comparta directamente la interfaz de red del host. Esto significa que cualquier referencia a `localhost` dentro del contenedor apunta al mismo host físico. Esta configuración es necesaria en esta práctica porque el frontend tiene hardcodeada la URL `http://localhost:3100` para conectarse al backend; sin `network_mode: host`, cada contenedor tendría su propio `localhost` aislado y la comunicación entre servicios fallaría.

---

## 4. Conocimientos previos

Para realizar esta práctica el estudiante necesita tener claro los siguientes temas:

- Uso básico de la terminal de Linux, Mac o Windows (WSL)
- Conceptos fundamentales de Docker (imágenes, contenedores, puertos)
- Comandos Docker (`build`, `run`, `compose up`, `logs`, `ps`)
- Fundamentos de Node.js y npm (`install`, `run build`, `run preview`)
- Estructura de un proyecto React con Vite y TypeScript
- Manejo del navegador web para verificar resultados

---

## 5. Objetivos a alcanzar

- Clonar y analizar la estructura de una aplicación React con Vite y su Mock API
- Crear un Dockerfile para el frontend React que compile y sirva la aplicación con Vite Preview
- Crear un Dockerfile para el servicio Mock API basado en json-server
- Redactar un archivo `compose.yaml` para orquestar ambos servicios
- Construir las imágenes Docker con `docker compose build`
- Levantar los contenedores con `docker compose up` y verificar su estado
- Acceder a la aplicación desde el navegador en `http://localhost:4173`

---

## 6. Equipo necesario

- Computador con sistema operativo Linux, Windows (WSL) o Mac
- Terminal de comandos
- Docker instalado (versión 24.x o superior)
- Docker Compose Plugin instalado (incluido en Docker Desktop)
- Editor de código (VS Code u otro)
- Navegador web (Chrome, Firefox, etc.)
- Conexión a internet (para clonar repositorios y descargar imágenes base de Docker Hub)

---

## 7. Material de apoyo

- Documentación oficial de Docker: <https://docs.docker.com>
- Referencia de Dockerfile: <https://docs.docker.com/reference/dockerfile/>
- Documentación de Docker Compose: <https://docs.docker.com/compose/>
- Repositorio frontend: <https://github.com/Daviddotcoms/suda-frontend-s6>
- Repositorio Mock API: <https://github.com/Daviddotcoms/mockAPI>
- Documentación de Vite: <https://vitejs.dev>
- Paquete json-server: <https://www.npmjs.com/package/json-server>
- Guía de la asignatura semana 7

---

## 8. Procedimiento

### Paso 1: Clonar los repositorios

Se clonaron los dos repositorios necesarios para la práctica:

```bash
git clone https://github.com/Daviddotcoms/suda-frontend-s6
git clone https://github.com/Daviddotcoms/mockAPI
```

El repositorio `suda-frontend-s6` contiene la aplicación React con Vite, TypeScript y Tailwind CSS. El repositorio `mockAPI` contiene el backend simulado basado en json-server que expone una API REST en el puerto `3100` a partir del archivo `db.json`.

---

### Paso 2: Verificar el funcionamiento local del frontend

Antes de contenedorizar, se verificó que el proyecto compilara correctamente de forma local:

```bash
cd suda-frontend-s6
npm install
npm run build
```

El build finalizó exitosamente transformando 182 módulos y generando los archivos optimizados en el directorio `dist/`, confirmando que el código fuente no contiene errores de compilación ni de tipos TypeScript.

---

### Paso 3: Crear el Dockerfile del frontend

Se creó el archivo `Dockerfile` en la raíz del proyecto `suda-frontend-s6` con el siguiente contenido:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .
RUN npm run build

EXPOSE 4173

CMD ["npm", "run", "preview", "--", "--host", "0.0.0.0", "--port", "4173"]
```

Este Dockerfile utiliza `node:20-alpine` como imagen base por su tamaño reducido. Copia primero solo los archivos `package*.json` para aprovechar el caché de capas de Docker en instalaciones sucesivas. Luego instala dependencias, compila la aplicación y la sirve con `vite preview` pasando `--host 0.0.0.0` para que sea accesible desde fuera del contenedor.

---

### Paso 4: Crear el Dockerfile del Mock API

Se creó el archivo `Dockerfile` en la raíz del proyecto `mockAPI`:

```dockerfile
FROM node:20-alpine

WORKDIR /app

COPY package*.json ./
RUN npm install

COPY . .

EXPOSE 3100

CMD ["npm", "start"]
```

El comando `npm start` ejecuta `json-server db.json --port 3100`, levantando la API REST simulada con los datos del archivo `db.json` en el puerto `3100`.

---

### Paso 5: Crear el archivo `compose.yaml`

Se creó el archivo `compose.yaml` en el directorio raíz del proyecto para orquestar ambos servicios. Se utilizó `network_mode: host` dado que el frontend tiene la URL del backend hardcodeada como `http://localhost:3100`:

```yaml
services:

  mock-api:
    build:
      context: ./mockAPI
    container_name: suda-mock-api
    ports:
      - "3100:3100"
    network_mode: host

  frontend:
    build:
      context: ./suda-frontend-s6
    container_name: suda-frontend
    ports:
      - "4173:4173"
    depends_on:
      - mock-api
    network_mode: host
```

La directiva `depends_on` garantiza que el contenedor del Mock API arranque antes que el frontend. La opción `network_mode: host` permite que ambos contenedores compartan la red del host, de manera que las peticiones a `http://localhost:3100` se resuelven correctamente sin modificar el código fuente.

---

### Paso 6: Construir las imágenes con Docker Compose

Se ejecutó el comando de build para construir ambas imágenes a partir de los Dockerfiles:

```bash
docker compose build
```

Docker Compose procesó ambos Dockerfiles y generó las imágenes correspondientes. En builds sucesivos se observó el uso de caché de capas (`CACHED`) para las instrucciones que no habían cambiado, reduciendo significativamente el tiempo de construcción. Al finalizar se crearon dos imágenes:

| Imagen | Descripción |
|---|---|
| `suda-frontend-s6-frontend` | Imagen del frontend React con Vite Preview en puerto 4173 |
| `suda-frontend-s6-mock-api` | Imagen del backend simulado con json-server en puerto 3100 |

![Ejecución de docker compose build mostrando la construcción de ambas imágenes con uso de caché, y listado de imágenes con docker image ls](./imagen1.png)

*Figura 7-1. Ejecución de `docker compose build` mostrando la construcción exitosa de ambas imágenes con uso de caché de capas, y confirmación con `docker image ls`.*

---

### Paso 7: Levantar los contenedores y verificar los logs

Se levantaron los contenedores en segundo plano y se verificó su estado:

```bash
docker compose up -d
docker compose ps
docker logs suda-frontend
```

Los contenedores arrancaron correctamente. Los logs del contenedor `suda-frontend` confirmaron que Vite Preview inició exitosamente, mostrando las URLs de acceso disponibles tanto en `localhost` como en las IPs de red del host.

![Panel izquierdo con docker compose up -d, docker compose ps y logs del contenedor. Panel derecho con los Dockerfiles del frontend y mock API en VS Code](./imagen2.png)

*Figura 7-2. Panel izquierdo: ejecución de `docker compose up -d`, estado de contenedores con `docker compose ps`, y logs del contenedor frontend mostrando Vite Preview activo en el puerto 4173. Panel derecho: contenido de los Dockerfiles del frontend (puerto 4173) y del Mock API (puerto 3100) visualizados en VS Code.*

---

### Paso 8: Verificar la aplicación en el navegador

Se abrió el navegador y se accedió a la URL:

```
http://localhost:4173
```

La aplicación cargó correctamente mostrando el listado de compañeros de clase con sus fotografías, nombres y correos institucionales. Esto confirmó que el frontend se comunica exitosamente con el Mock API en el puerto `3100`, obteniendo los datos del archivo `db.json` a través de la red compartida del host.

![Aplicación React ejecutándose en http://localhost:4173 mostrando el listado de estudiantes con fotos y correos](./imagen3.png)

*Figura 7-3. Aplicación React ejecutándose en `http://localhost:4173` desde el contenedor Docker, mostrando el listado de estudiantes consumido desde el Mock API en `http://localhost:3100`.*

---

### Paso 9: Detener los contenedores (opcional)

Para detener y eliminar los contenedores cuando ya no sean necesarios:

```bash
docker compose down
```

---

## 9. Resultados esperados

Al finalizar la práctica se obtuvieron los siguientes resultados:

- Se clonaron y analizaron correctamente los repositorios `suda-frontend-s6` y `mockAPI`, identificando la estructura del proyecto React con Vite y el backend simulado con json-server

- Se creó un `Dockerfile` para el frontend que instala dependencias, compila la aplicación con `npm run build` y la sirve mediante `vite preview` en el puerto `4173` con acceso externo habilitado mediante `--host 0.0.0.0`

- Se creó un `Dockerfile` para el Mock API que levanta `json-server` sobre el archivo `db.json` en el puerto `3100`

- Se definió un archivo `compose.yaml` con ambos servicios configurados con `network_mode: host` para garantizar la comunicación entre contenedores a través de `localhost`, sin necesidad de modificar el código fuente del frontend

- Se construyeron exitosamente las imágenes `suda-frontend-s6-frontend` y `suda-frontend-s6-mock-api` con `docker compose build`, observando el uso de caché de capas en builds sucesivos que reduce el tiempo de construcción

- Los contenedores se levantaron con `docker compose up -d` y los logs confirmaron que Vite Preview inició correctamente en el puerto `4173`

- La aplicación fue verificada desde el navegador en `http://localhost:4173`, mostrando el listado completo de compañeros con datos obtenidos desde el Mock API, confirmando la comunicación end-to-end dentro del entorno contenedorizado

Como resultado general, se logró contenedorizar una aplicación React real junto con su backend simulado, comprendiendo la construcción de imágenes Docker, la redacción de Dockerfiles optimizados con caché de capas, y la orquestación de múltiples servicios con Docker Compose.

---

## 10. Bibliografía

- Docker Inc. (2025). *Docker overview*. Docker Documentation. <https://docs.docker.com/get-started/overview/>
- Docker Inc. (2025). *Dockerfile reference*. Docker Documentation. <https://docs.docker.com/reference/dockerfile/>
- Docker Inc. (2025). *Docker Compose overview*. Docker Documentation. <https://docs.docker.com/compose/>
- Docker Inc. (2025). *Networking in Compose*. Docker Documentation. <https://docs.docker.com/compose/how-tos/networking/>
- Docker Inc. (2025). *network_mode - Compose Deploy Specification*. Docker Documentation. <https://docs.docker.com/reference/compose-file/services/#network_mode>
- Vite. (2025). *Vite – Next Generation Frontend Tooling*. <https://vitejs.dev>
- npm. (2025). *json-server*. <https://www.npmjs.com/package/json-server>
- Node.js Foundation. (2025). *Node.js Documentation*. <https://nodejs.org/docs>
- Guía de la asignatura – Semana 7.
