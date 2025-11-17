# Laboratorios de Big Data

> [!IMPORTANT]
>
> Cada estudiante debe documentar y adjuntar toda la evidencia de los
> laboratorios de Big Data en su repositorio de GitHub para la asignatura.
> Además, debe compartir con el profesor, mediante Interactiva Virtual, la URL
> exacta del repositorio y del directorio donde trabaja los laboratorios.

## Conexión al clúster

### Amazon EMR

Cree su propio clúster siguiendo los videos guía sobre la creación y el uso de
Amazon EMR con la cuenta de AWS Academy. Recuerde verificar los prerequisitos de
red, seguridad y configuración antes de ejecutar cualquier laboratorio.

> [!TIP]
>
> Usted puede reutilizar las configuraciones de clúster como plantillas para
> acelerar futuras implementaciones y garantizar consistencia en los ejercicios.

## Datasets

Nos referiremos a los [`datasets`](../../datasets) a lo largo de los tutoriales.

## Creación y gestión de clústeres Amazon EMR

Utilizando la cuenta de AWS Academy y el material suministrado, usted debe aprender a configurar:

- Clústeres Amazon EMR
- AWS CLI
- Almacenamiento de archivos en S3
- Notebooks Jupyter

## Laboratorios

### Gestión de archivos en S3 y HDFS


> [!NOTE]
>
> Mantenga capturas de pantalla, scripts y bitácoras para cada laboratorio. Esto facilitará la evaluación y servirá como referencia para futuros proyectos.

> [!NOTE]
>
> Documente todos los laboratorios con la plantilla oficial: [st0263-252-labs-student.docx](st0263-252-labs-student.docx).

- [Lab3-1: Gestión de archivos entre HDFS y S3](01-hdfs/README.md)
  Copie los archivos de [datasets](../../datasets) tanto en S3 (almacenamiento permanente) como en HDFS (almacenamiento temporal).

- [Lab3-2: Datalake con AWS S3, Glue y Athena](02-athena/lab-s3-glue-athena.pdf)

- [Lab3-3: Hadoop con motor de consulta Hive integrado con S3, Glue y Athena](03-hive/README.md)

- [Lab3-4: Hadoop Spark desde EMR, JupyterHub y Google Colab (Spark SQL y DataFrames)](04-spark/README.md)
