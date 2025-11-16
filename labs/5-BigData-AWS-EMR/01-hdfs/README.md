# Laboratorio 3-1: Usar el HDFS

## Resumen de lo que sigue

En este cluster copiar archivos:

| Por | Hacia |
|---|---|
| Hue | HDFS |
| SSH | HDFS |
| Hue | S3 |
| SSH | S3 |

Recordar que los de HDFS son efímeros o temporales, es decir, se borran cuando
se borra el clúster.

En esta infraestructura, deberá realizar el copiado de
[`datasets`](../../../datasets).

## 1. Conectarse al nodo principal del clúster

Cada quien tiene su propio servidor EC2 del máster en EMR:

```bash
ssh -i ~/vockey.pem hadoop@ec2.compute-1.amazonaws.com
```

Clonar este repositorio (pues contiene los datasets).

Posiblemente necesite instalar git con el comando `sudo dnf install git`.

> [!NOTE]
>
> Puede encontrar los datasets en el directorio [`datasets`](../../../datasets).

## 2. Gestión de archivos en HDFS vía la terminal

1. Cargar los datos de los datasets de trabajo del tutorial en HDFS.
2. Crear un directorio `datasets` en su 'home' (p. ej. `/user/hadoop`) en HDFS.
3. En `datasets` los archivos ya deben estar descomprimidos para ser procesables.

### Listar archivos en HDFS

> [!NOTE]
>
> Para efectos de esta guía, es equivalente `hadoop fs` y `hdfs dfs`. La
> diferencia es que `hdfs dfs` es solo para sistemas de archivos HDFS, pero
> `hadoop fs` soporta otros adicionales como Amazon S3.


```shell
hdfs dfs -ls /
hdfs dfs -ls /user
hdfs dfs -ls /user/hadoop
hdfs dfs -ls /user/hadoop/datasets  # Verá que este directorio no existe.
```

### Crear un directorio `datasets` en HDFS

```shell
hdfs dfs -mkdir /user/hadoop/datasets
```

### Copiar archivos locales (al servidor gateway) hacia HDFS

> [!NOTE]
> Se asume que tiene los datos:
> 1. Locales en `datasets` en el gateway (nodo maestro).
> 2. También están [en este repositorio](../../../datasets/),
>    y debería copiarlos por SCP al servidor gateway por la VPN (?).
> 3. También están en Amazon S3: `s3://${bucket_name}/datasets`

```shell
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg-small
```

* Archivos locales en el filesytem del nodo maestro:

```shell
hdfs dfs -put ~/st0263/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg-small/
hdfs dfs -ls /user/hadoop/datasets/gutenberg-small/
```

* Archivos en Amazon S3:

```shell
hadoop distcp s3://${username_datalake}/datasets/airlines.csv /tmp/
hdfs dfs -ls /tmp/
```

* Copia recursiva de datos:

```shell
hdfs dfs -copyFromLocal ~/st0263/datasets/* /user/hadoop/datasets/
```

* Listar archivos:

```shell
hdfs dfs -ls /user/hadoop/datasets
hdfs dfs -ls /user/hadoop/datasets/gutenberg-small/
```

### Copiar archivos de HDFS hacia el servidor local (gateway)

```shell
hdfs dfs -get /user/hadoop/datasets/gutenberg-small/* ~<username>/mis_datasets/
```
El directorio `mis_datasets` debe estar creado.

Otro comando para traer los datos:

```shell
hdfs dfs -copyToLocal /user/hadoop/datasets/gutenberg/gutenberg-small.zip ~<username>/mis_datasets/

ls -l mis_datasets
```

### Probar otros commandos

Se aplica los siguientes comandos a:

```shell
hdfs dfs -<command>
```

Otros comandos:

```
du <path>             uso de disco en bytes
mv <src> <dest>       mover archive(s)
cp <src> <dest>       copiar archivo(s)
rm <path>             borrar archive(s)
put <localSrc> <dest-hdfs> copiar local a hdfs
cat <file-name>       mostrar contenido de archivo
chmod [-R] mode       cambiar los permisos de un archivo
chown <username> files   cambiar el dueño de un archivo
chgrp <group> files      cambiar el grupo de un archivo
```

## 3. Gestión de archivos vía Hue

### Login

![login](screenshots/hue-01-login.png)

![filemenu](screenshots/hue-02-Files.png)

### Explorar archivos

![explorar](screenshots/hue-03-FileBrowser.png)

### Crear un directorio

![Crear directorio](screenshots/hue-04-FileNew.png)

![Crear directorio](screenshots/hue-05-FileNewDir1.png)

![Crear directorio](screenshots/hue-06-FileNewDir2.png)

### Subir archivos

![Subir archivos](screenshots/hue-07-FileUpload1.png)

![Subir archivos](screenshots/hue-08-FileUpload2.png)

![Subir archivos](screenshots/hue-09-FileUpload3.png)

![Subir archivos](screenshots/hue-10-FileBrowser.png)

### Ver contenido de un archivo

![Ver archivo](screenshots/hue-11-FileOpen.png)
