# Laboratorio 3-4: Spark

## Contextualización de diferentes formas de correr Spark

### 1. De forma interactiva via `pyspark`

Inicie PySpark:

```shell
$ pyspark
>>>
```

Ya trae preconfigurado las variables `spark` y `sc`.

Así salva la variable `wc` un archivo por
[Resilient Distributed Dataset (RDD)](https://www.databricks.com/glossary/what-is-rdd).

```python
>>> files = sc.textFile("s3://${bucket_name}/datasets/gutenberg-small/*.txt")
>>> wc = files.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
>>> wc.saveAsTextFile("hdfs:///tmp/wcount1")
```

Si quiere que se consolide en un solo archivo de salida:

```python
>>> wc.coalesce(1).saveAsTextFile("hdfs:///tmp/wcount2")
```

## 2. Como un script de Python

Ver, por ejemplo, el script [`wc-pyspark.py`](notebooks/wc-pyspark.py).

Córralo con el comando:

```shell
spark-submit \
  --master yarn \
  --deploy-mode cluster \
  wc-pyspark.py
```

## 3. Desde Zeppelin

Entre desde un navegador a Zeppelin. Si está usando AWS EMR, por defecto no tiene que iniciar sesión.

### Python

```python
%spark2.pyspark
files = sc.textFile("s3://${bucket_name}/datasets/gutenberg-small/*.txt")
wc = files.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
wc.coalesce(1).saveAsTextFile("hdfs:///tmp/wcount2")
```

### SparkSQL

```sql
%spark2.sql
SHOW tables
```

```sql
%spark2.sql
SHOW database
```

```sql
%spark2.sql
USE <user_vpn>
```

```sql
%spark2.sql
CREATE EXTERNAL TABLE
<user_vpn>.docs2 (line STRING)
STORED AS textfile LOCATION '/datasets/gutenberg-small/*.txt'
```

```sql
%spark2.sql
SELECT word, count(1)
AS count
FROM (SELECT explode(split(line,' ')) AS word FROM docs2) w
GROUP BY word
ORDER BY word
```

## 4. Jupyter Notebooks

Ver, por ejemplo, [`wordcount-spark.ipynb`](notebooks/wordcount-spark.ipynb)

## Dataframes y preparación de datos en pyspark

- Notebook: [Data_processing_using_PySpark.ipynb](notebooks/Data_processing_using_PySpark.ipynb)

- Datos de ejemplo: [sample_data.csv](../../../datasets/spark/sample_data.csv)

## Manejo de notebooks en EMR

Tiene varias opciones:

- A través del servicio JupyterHub como componente de EMR

- Notebooks como servicio de AWS para EMR (opción a trabajar)

Para trabajar con los notebooks gestionados por AWS, la gestión de paquetes,
versión de Python puede ser consultado en
[la documentación de AWS](https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-scoped-libraries.html).

### Opciones importantes

#### Cambiar versión de Python

```shell
%%configure -f
{ "conf":{
"spark.pyspark.python": "python3",
"spark.pyspark.virtualenv.enabled": "true",
"spark.pyspark.virtualenv.type":"native",
"spark.pyspark.virtualenv.bin.path":"/usr/bin/virtualenv"
}}
```

#### Listar bibliotecas actuales

```python
sc.list_packages()
```

#### Instalar una biblioteca

```python
sc.install_pypi_package("nltk")
```

#### Desinstalar una biblioteca

```python
sc.uninstall_package("nltk")
```

## Actividades del laboratorio

### 1. Ejecutar todos los notebooks del directorio actual

1. Ejecutar
  [`Data_processing_using_PySpark.ipynb`](notebooks/Data_processing_using_PySpark.ipynb)
  en:
  - EMR con datos en AWS S3.
  - Google Colab con datos en Google Drive.
  - Google Colab con datos en AWS S3.

2. Ejecutar [`spark_colab_ejercicios`](notebooks/spark_colab_ejercicios.ipynb) en:
  - EMR con datos en AWS S3.
  - Google Colab con datos en Google Drive.

3. Ejecutar [`wordcount-spark.ipynb`](notebooks/wordcount-spark-colab.ipynb) en EMR.

4. Ejecutar [`wordcount-spark-colab.ipynb`](notebooks/wordcount-spark-colab.ipynb) en
  Google Colab.

5. Ejecutar [`wc-pyspark.py`](notebooks/wc-pyspark.py) en el nodo maestro de EMR.

> [!WARNING]
>
> Para las evidencias, adjunte los notebooks con las celdas ejecutadas en cada
> ambiente solicitado. Si no es posible, aporte pantallazos que demuestren la
> ejecución completa.

### 2. Basarse en los datos de COVID-19 para realizar procesamiento

#### Procesamiento básico con DataFrames

1. Cargar datos CSV en Spark desde un bucket S3 (EMR con notebooks administrados
   o JupyterHub), desde Google Colab y, opcionalmente, mediante boto3.
2. Borrar y crear columnas según sea necesario.
3. Aplicar filtros que resulten interesantes para el análisis.
4. Realizar agrupaciones y consultas categóricas (por ejemplo, casos por región o
   sexo/género).
5. Guardar los resultados en un bucket público de S3.
6. Documentar todo el flujo dentro del notebook.

> [!TIP]
>
> Los datos pueden descargarse desde
> [este enlace](https://www.datos.gov.co/Salud-y-Protecci-n-Social/Casos-positivos-de-COVID-19-en-Colombia/gt2j-8ykr/data).

> [!NOTE]
>
> El directorio [`datasets`](../../../datasets) contiene ejemplos locales.

#### Preguntas de negocio con DataFrames y SparkSQL

1. Los 10 departamentos con más casos de COVID-19 en Colombia, ordenados de
  mayor a menor.
2. Las 10 ciudades con más casos de COVID-19 en Colombia, ordenadas de mayor a
  menor.
3. Los 10 días con más casos de COVID-19 en Colombia, ordenados de mayor a
   menor.
4. Distribución de casos por edades en Colombia.
5. Una pregunta de negocio adicional definida por usted y resuelta en Spark.

### Requerimientos de salida y entrega

- Resuelva cada pregunta tanto con DataFrames como con SparkSQL.
- Guarde los resultados en formatos `.csv` y `.parquet`, almacenados en S3 y en
Google Drive según el ambiente de ejecución.
- Entregue los notebooks de Colab y EMR con todas las celdas ejecutadas.
