# Práctica No. 1 - Comandos de Linux

## 1. Título

Creación y manipulación de archivos y directorios en Linux mediante comandos básicos

## 2. Tiempo de duración

60 minutos aproximadamente

## 3. Fundamentos

Linux es un sistema operativo de código abierto basado en Unix que permite la administración completa de archivos, procesos y recursos del sistema mediante comandos en la terminal. A diferencia de sistemas operativos con interfaz gráfica como Windows, Linux se basa en una interacción directa con el sistema a través de texto, lo que brinda mayor control, eficiencia y capacidad de automatización de tareas.

Uno de los conceptos principales es la **estructura de directorios**, la cual es jerárquica y comienza desde el directorio raíz (`/`). Dentro de este se organizan todas las carpetas y archivos del sistema. Para gestionar esta estructura se utilizan comandos como `mkdir` para crear directorios, `cd` para navegar entre ellos y `ls` para listar su contenido.

La **manipulación de archivos** es otro aspecto fundamental en Linux. El sistema permite crear archivos con el comando `touch` o mediante editores como `nano` o `nvim`, copiar archivos con `cp`, moverlos o renombrarlos con `mv`, y eliminarlos con `rm`. Estas operaciones permiten organizar la información del sistema de manera eficiente sin necesidad de una interfaz gráfica.

La **redirección de entrada y salida** es una característica muy poderosa de Linux. Con el operador `>` se puede redirigir la salida de un comando hacia un archivo, sobrescribiendo su contenido anterior. El operador `>>` permite agregar información al final de un archivo sin borrar lo que ya existe. Esto es fundamental para automatizar tareas, crear registros y manipular datos desde la terminal.

El comando `cat` es una herramienta versátil que permite visualizar el contenido de archivos en la terminal y también redirigir información entre ellos. Su nombre viene de "concatenate" (concatenar), ya que puede unir el contenido de múltiples archivos en uno solo.

Los **editores de texto en terminal** como `nano` y `nvim` permiten crear y modificar archivos directamente desde la línea de comandos sin depender de un entorno gráfico. `nano` es más intuitivo para principiantes, mientras que `nvim` (NeoVim) es una versión mejorada de `vim` que ofrece mayor potencia y personalización para usuarios avanzados.

Finalmente, el comando `history` permite visualizar el historial de comandos ejecutados en la sesión actual, y combinado con el operador `>` o tuberías (`|`), permite exportar ese historial a un archivo de texto, lo cual es muy útil para documentar prácticas y auditar el trabajo realizado.

Estos conocimientos son esenciales para cualquier área relacionada con sistemas, desarrollo de software, administración de servidores o ciberseguridad.

## 4. Conocimientos previos

Para realizar esta práctica el estudiante necesita tener claro los siguientes temas:

- Manejo básico de la terminal de Linux
- Navegación entre directorios con `cd` y `ls`
- Concepto de rutas absolutas y relativas
- Uso de comandos básicos (`mkdir`, `touch`, `cp`, `mv`, `rm`)
- Uso de editores de texto en terminal (nano o nvim)

## 5. Objetivos a alcanzar

- Crear y organizar una estructura de directorios en Linux
- Manipular archivos mediante comandos (crear, copiar, mover y eliminar)
- Aplicar redirección de contenido entre archivos con `>` y `>>`
- Comprender el uso de comandos básicos en la terminal
- Gestionar información de forma eficiente mediante la línea de comandos
- Exportar el historial de comandos a un archivo de texto

## 6. Equipo necesario

- Computador con sistema operativo Linux, Windows (WSL) o GitBash
- Terminal de comandos
- Editor de texto en terminal (nano o nvim)
- Cuenta en GitHub
- Conexión a internet

## 7. Material de apoyo

- Guía de la práctica proporcionada por el docente
- Apuntes de clase sobre comandos Linux
- Cheat sheet de comandos básicos Linux
- Documentación oficial: `man` pages (ej. `man cp`, `man mv`)

## 8. Procedimiento

### Paso 1: Crear estructura de carpetas

Se creó la carpeta principal `proyecto_comandos` y dentro de ella las tres subcarpetas requeridas: `documentos`, `imagenes` y `scripts`.

```bash
mkdir proyecto_comandos
cd proyecto_comandos
mkdir documentos
mkdir imagenes
mkdir scripts
```

### Paso 2: Crear el archivo notas.txt

Se navegó a la carpeta `documentos` y se creó el archivo `notas.txt` utilizando el editor `nvim`. Se escribieron tres líneas de texto dentro del archivo.

```bash
cd documentos
nvim notas.txt
```

Contenido del archivo `notas.txt`:

```
hola
hola x2
hola x3
```

### Paso 3: Copiar archivo a scripts con nuevo nombre

Se copió el archivo `notas.txt` hacia la carpeta `scripts`, renombrándolo como `backup_notas.txt` en el mismo comando.

```bash
cp notas.txt /home/guasmo/Documents/proyecto_comandos/scripts/backup_notas.txt
```

### Paso 4: Mover archivo a imagenes

Se movió el archivo `backup_notas.txt` desde la carpeta `scripts` hacia la carpeta `imagenes`.

```bash
mv /home/guasmo/Documents/proyecto_comandos/scripts/backup_notas.txt /home/guasmo/Documents/proyecto_comandos/imagenes/
```

### Paso 5: Crear archivo resumen.txt

Desde la carpeta `documentos`, se creó un archivo vacío llamado `resumen.txt`.

```bash
cd ../documentos
touch resumen.txt
```

### Paso 6: Redireccionar contenido de notas.txt a resumen.txt

Se redireccionó el contenido completo de `notas.txt` hacia `resumen.txt`, sobrescribiendo cualquier contenido previo.

```bash
cat notas.txt > resumen.txt
```

### Paso 7: Añadir nueva línea sin sobrescribir

Se añadió una cuarta línea al final de `resumen.txt` sin eliminar el contenido existente, usando el operador `>>`.

```bash
echo "hola x4" >> resumen.txt
```

Contenido resultante de `resumen.txt`:

```
hola
hola x2
hola x3
hola x4
```

### Paso 8: Eliminar el archivo backup_notas.txt

Se navegó a la carpeta `imagenes` y se eliminó el archivo `backup_notas.txt`.

```bash
cd ../imagenes
rm backup_notas.txt
```

### Paso 9: Eliminar la carpeta imagenes

Al estar vacía la carpeta `imagenes`, se procedió a eliminarla desde el directorio padre.

```bash
cd ..
rm -rf imagenes/
```

### Paso 10: Exportar historial de comandos

Se exportó el historial completo de la sesión a un archivo de texto con el nombre y apellido del estudiante.

```bash
history > tarea-s1-juan_buri.txt
```

## 9. Resultados esperados

Al finalizar la práctica se obtuvieron los siguientes resultados:

- Se creó correctamente la estructura de carpetas `proyecto_comandos/documentos`, `proyecto_comandos/imagenes` y `proyecto_comandos/scripts`
- Se generó el archivo `notas.txt` con tres líneas de contenido usando el editor `nvim`
- Se realizó correctamente la operación de copia del archivo con cambio de nombre usando `cp`
- Se movió el archivo entre directorios correctamente con `mv`
- Se creó `resumen.txt` y se le redireccionó el contenido de `notas.txt` usando `>`
- Se añadió una nueva línea al archivo sin sobrescribir el contenido existente con `>>`
- Se eliminó el archivo `backup_notas.txt` y la carpeta `imagenes` correctamente
- Se generó el archivo `tarea-s1-juan_buri.txt` con el historial de comandos de la sesión

Como resultado general, se logró comprender y aplicar correctamente los comandos básicos de Linux para la gestión de archivos y directorios, así como el uso de redirección de contenido, fortaleciendo habilidades prácticas esenciales en el uso de la terminal.

## 10. Bibliografía
