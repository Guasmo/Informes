# Práctica No. 3 - Persistencia de Datos con Volúmenes Docker y PostgreSQL

## 1. Título

Creación de volúmenes Docker para persistir bases de datos PostgreSQL

## 2. Tiempo de duración

90 minutos aproximadamente

## 3. Fundamentos

**Docker** es una plataforma de contenedorización que permite ejecutar aplicaciones en entornos aislados llamados contenedores. Una de las características más importantes a entender cuando se trabaja con bases de datos en Docker es el concepto de **persistencia de datos**.

Por defecto, los contenedores Docker son **efímeros**: cuando un contenedor se elimina, todos los datos que contenía desaparecen con él. Esto ocurre porque el sistema de archivos de un contenedor vive únicamente mientras el contenedor existe. Esta característica es útil para aplicaciones sin estado, pero representa un problema grave cuando se trabaja con bases de datos, ya que toda la información almacenada se perdería al eliminar o recrear el contenedor.

Para solucionar este problema, Docker introduce el concepto de **volúmenes**. Un volumen es un mecanismo de almacenamiento persistente que vive fuera del ciclo de vida del contenedor. Los volúmenes son gestionados directamente por Docker y se almacenan en una ruta del sistema anfitrión (`/var/lib/docker/volumes/` en Linux). Al asociar un volumen a un contenedor, los datos escritos en esa ruta del contenedor se guardan en el volumen, y aunque el contenedor sea eliminado, el volumen y su contenido permanecen intactos. Cuando se crea un nuevo contenedor asociado al mismo volumen, los datos siguen disponibles.

**PostgreSQL** es uno de los sistemas de gestión de bases de datos relacionales más robustos y utilizados en el mundo. Es de código abierto, compatible con el estándar SQL y soporta características avanzadas como transacciones ACID, claves foráneas, vistas, índices y procedimientos almacenados. PostgreSQL almacena sus datos en un directorio de datos llamado `PGDATA`, que en la imagen oficial de Docker se encuentra en `/var/lib/postgresql`.

Al ejecutar PostgreSQL en Docker, si no se asocia un volumen, los datos de la base de datos solo existen dentro del contenedor. Si el contenedor se elimina, la información se pierde permanentemente. En cambio, si se monta un volumen en `/var/lib/postgresql`, PostgreSQL escribe sus datos en el volumen, y estos sobreviven a la eliminación del contenedor.

La imagen oficial de PostgreSQL en Docker Hub a partir de la versión 18 requiere que el volumen se monte en `/var/lib/postgresql` en lugar de `/var/lib/postgresql/data`, ya que las versiones recientes organizan los datos en subdirectorios versionados para facilitar actualizaciones con `pg_upgrade`.

Comprender la diferencia entre contenedores con y sin volúmenes es fundamental para cualquier entorno de producción donde la pérdida de datos no es aceptable.

## 4. Conocimientos previos

Para realizar esta práctica el estudiante necesita tener claro los siguientes temas:

- Uso básico de la terminal de Linux
- Conceptos básicos de Docker (imágenes, contenedores, puertos)
- Comandos básicos de Docker (`run`, `ps`, `stop`, `rm`, `logs`)
- Conceptos básicos de bases de datos relacionales (tablas, registros, SQL)
- Uso básico de un cliente PostgreSQL (`psql` o herramienta gráfica)

## 5. Objetivos a alcanzar

- Demostrar la pérdida de datos en contenedores Docker sin volúmenes
- Crear y gestionar volúmenes Docker para persistencia de datos
- Desplegar un contenedor PostgreSQL asociado a un volumen
- Crear bases de datos, tablas e insertar registros usando `psql`
- Verificar que los datos persisten tras eliminar y recrear el contenedor

## 6. Equipo necesario

- Computador con sistema operativo Linux, Windows (WSL) o Mac
- Terminal de comandos
- Docker instalado (versión 20.x o superior)
- Cliente PostgreSQL instalado (`psql`) o herramienta gráfica (DataGrip, TablePlus)
- Conexión a internet

## 7. Material de apoyo

- Documentación oficial de Docker Volumes: https://docs.docker.com/storage/volumes/
- Documentación oficial de PostgreSQL: https://www.postgresql.org/docs/
- Imagen oficial de PostgreSQL en Docker Hub: https://hub.docker.com/_/postgres
- Guía de la asignatura

## 8. Procedimiento

---

### 🔴 Parte 1: Base de datos SIN volumen

#### Paso 1: Crear el contenedor PostgreSQL sin volumen

Se creó el primer contenedor llamado `server_db1` sin asociar ningún volumen:

```bash
docker run --name server_db1 \
  -e POSTGRES_PASSWORD=1234 \
  -p 5432:5432 \
  -d postgres
```

#### Paso 2: Conectarse al contenedor con psql

```bash
psql -h localhost -U postgres
```

#### Paso 3: Crear la base de datos test

```sql
CREATE DATABASE test;
```

#### Paso 4: Conectarse a la base de datos test y crear la tabla customer

```sql
\c test

CREATE TABLE customer (
    id SERIAL PRIMARY KEY,
    fullname VARCHAR(100),
    status VARCHAR(50)
);
```

#### Paso 5: Insertar un registro en la tabla

```sql
INSERT INTO customer (fullname, status)
VALUES ('Juan Buri', 'activo');
```

#### Paso 6: Verificar que el registro existe

```sql
SELECT * FROM customer;
```

Resultado:

```
 id | fullname  | status 
----+-----------+--------
  1 | Juan Buri | activo
```

#### Paso 7: Salir, detener y eliminar el contenedor

```bash
\q

docker stop server_db1
docker rm server_db1
```

#### Paso 8: Recrear el contenedor con el mismo nombre

```bash
docker run --name server_db1 \
  -e POSTGRES_PASSWORD=1234 \
  -p 5432:5432 \
  -d postgres
```

#### Paso 9: Verificar que los datos ya no existen

```bash
psql -h localhost -U postgres
```

```sql
\l
```

**Resultado:** La base de datos `test` ya no aparece en la lista. Los datos se perdieron al eliminar el contenedor, confirmando que sin volúmenes no hay persistencia.

```bash
\q
docker stop server_db1
docker rm server_db1
```

---

### 🟢 Parte 2: Base de datos CON volumen

#### Paso 10: Eliminar volumen anterior si existe y crear uno nuevo

```bash
docker volume rm pgdata 2>/dev/null
docker volume create pgdata
```

#### Paso 11: Crear el contenedor PostgreSQL con volumen

Se usó `/var/lib/postgresql` como punto de montaje (requerido en PostgreSQL 18+):

```bash
docker run --name server_db2 \
  -e POSTGRES_PASSWORD=1234 \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql \
  -d postgres
```

#### Paso 12: Conectarse y crear la base de datos

```bash
psql -h localhost -U postgres
```

```sql
CREATE DATABASE test;
\c test
```

#### Paso 13: Crear la tabla customer

```sql
CREATE TABLE customer (
    id SERIAL PRIMARY KEY,
    fullname VARCHAR(100),
    status VARCHAR(50)
);
```

#### Paso 14: Insertar un registro

```sql
INSERT INTO customer (fullname, status)
VALUES ('Juan Buri', 'activo');
```

#### Paso 15: Verificar el registro

```sql
SELECT * FROM customer;
```

Resultado:

```
 id | fullname  | status 
----+-----------+--------
  1 | Juan Buri | activo
```

#### Paso 16: Detener y eliminar el contenedor (conservando el volumen)

```bash
\q

docker stop server_db2
docker rm server_db2
```

#### Paso 17: Recrear el contenedor usando el mismo volumen

```bash
docker run --name server_db2 \
  -e POSTGRES_PASSWORD=1234 \
  -p 5432:5432 \
  -v pgdata:/var/lib/postgresql \
  -d postgres
```

#### Paso 18: Verificar que los datos persisten

```bash
psql -h localhost -U postgres
```

```sql
\l
\c test
SELECT * FROM customer;
```

**Resultado:** La base de datos `test`, la tabla `customer` y el registro de Juan Buri siguen presentes, confirmando que el volumen persistió los datos correctamente.

```
 id | fullname  | status 
----+-----------+--------
  1 | Juan Buri | activo
```

```bash
\q
```

---

### ⚠️ Nota: Error común en PostgreSQL 18+

Al intentar montar el volumen en `/var/lib/postgresql/data` con PostgreSQL 18, se producía el siguiente error:

```
Error: in 18+, these Docker images are configured to store database data in a
format which is compatible with "pg_ctlcluster"...
```

**Solución:** Cambiar el punto de montaje del volumen de `/var/lib/postgresql/data` a `/var/lib/postgresql`:

```bash
# ❌ Incorrecto para PostgreSQL 18+
-v pgdata:/var/lib/postgresql/data

# ✅ Correcto para PostgreSQL 18+
-v pgdata:/var/lib/postgresql
```

## 9. Resultados esperados

Al finalizar la práctica se obtuvieron los siguientes resultados:

- **Parte 1 (sin volumen):** Se confirmó que al eliminar el contenedor `server_db1`, la base de datos `test` y todos sus registros desaparecieron. Al recrear el contenedor, la instancia de PostgreSQL estaba completamente vacía, demostrando que sin volúmenes no existe persistencia de datos.

- **Parte 2 (con volumen):** Se confirmó que al eliminar el contenedor `server_db2` y recrearlo usando el mismo volumen `pgdata`, la base de datos `test`, la tabla `customer` y el registro insertado (`Juan Buri`, `activo`) seguían presentes e intactos, demostrando que los volúmenes Docker garantizan la persistencia de datos independientemente del ciclo de vida del contenedor.

- Se identificó y resolvió el error de compatibilidad de la imagen oficial de PostgreSQL 18+, que requiere montar el volumen en `/var/lib/postgresql` en lugar de `/var/lib/postgresql/data`.

Como resultado general, se comprendió la diferencia crítica entre almacenamiento efímero y persistente en Docker, y se estableció la práctica correcta para desplegar bases de datos en contenedores de producción.

## 10. Bibliografía
