# Laboratorio 3-1: Usar el HDFS

## Resumen de lo que sigue

En este cluster deben hacer:

* Copiar (gestión) de archivos hacia el HDFS vía HUE.

* Copiar (gestión) de archivos hacia el HDFS vía SSH.

Recuerden que estos datos de HDFS son efímeros o temporales y se borran cuando
se borre el clúster.

* Copiar (gestión) de archivos hacia AWS S3 vía HUE.

* Copiar (gestión) de archivos hacia el AWS S3 vía SSH.

En esta infraestructura, cada alumno deberá realizar el copiado de los archivos
datasets de [datasets](../../../datasets)

## 1. Conectarse al nodo principal del clúster

Cada quien tiene su propio servidor EC2 del máster en EMR:

```bash
ssh -i ~/vockey.pem hadoop@ec2.compute-1.amazonaws.com
```

Clonar este repositorio (pues contiene los datasets).

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
hdfs dfs -ls /user/hadoop/datasets
```

### Crear un directorio `datasets` en HDFS

```shell
hdfs dfs -mkdir /user/hadoop/datasets
```

### Copiar archivos locales (al servidor gateway) hacia HDFS

Se asume que tiene los datos **locales** en `datasets` en el gateway.

También están en este repositorio, y por terminal debería copiarlos por SSH/SCP
al servidor gateway por la VPN.

También están en Amazon S3: `s3://username_datalake/datasets`

```shell
hdfs dfs -mkdir /user/hadoop/datasets
hdfs dfs -mkdir /user/hadoop/datasets/gutenberg-small
```

* Archivos locales en el filesytem del nodo maestro:

```shell
hdfs dfs -put /home/ec2-home/st0263-252/bigdata/datasets/gutenberg-small/*.txt /user/hadoop/datasets/gutenberg-small/
```

* Archivos en Amazon S3:

```shell
hadoop distcp s3://username_datalake/datasets/airlines.csv /tmp/
```

* Copia recursiva de datos:

```shell
hdfs dfs -copyFromLocal /home/ec2-home/st0263-252/bigdata/datasets/* /user/hadoop/datasets/
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
