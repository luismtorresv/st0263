# Universidad EAFIT
# Curso ST0263 Tópicos Especiales en Telemática

# LAB SPARK

* Se puede programar en python, scala o java.

* ejemplos en python:

## 1. De forma interactiva via 'pyspark'

// ya trae preconfigurado las variables sc y spark

    $ pyspark
    >>> files = sc.textFile("s3://username_datalake/datasets/gutenberg-small/*.txt")
    >>> wc = files.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    >>> wc.saveAsTextFile("hdfs:///tmp/wcout1")

    * asi salva wc un archivo por rdd.
    * si quiere que se consolide en un solo archivo de salida:

    $ pyspark
    >>> files = sc.textFile("s3://username_datalake/datasets/gutenberg-small/*.txt")
    >>> wc = files.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    >>> wc.coalesce(1).saveAsTextFile("hdfs:///tmp/wcout2")

## 2. Como un archivo python: [wc-pyspark.py](wc-pyspark.py)

* correrlo:

    $ spark-submit --master yarn --deploy-mode cluster wc-pyspark.py

## 3. Desde Zeppelin Nodebook:

    * Entre desde un browser a: http://ip-or-name-amazon-host:9995 o el servidor que le desigen para el lab
    * Si es en EMR, por defecto NO tiene login/pass

### Wordcount en python:

```python
    %spark2.pyspark
    files = sc.textFile("s3://username_datalake/datasets/gutenberg-small/*.txt")
    wc = files.flatMap(lambda line: line.split(" ")).map(lambda word: (word, 1)).reduceByKey(lambda a, b: a + b)
    wc.coalesce(1).saveAsTextFile("hdfs:///tmp/wcout1")
```

### wordcount en spark.sql en zeppelin

    %spark2.sql
    SHOW tables

    %spark2.sql
    SHOW database

    %spark2.sql
    USE <user_vpn>

    %spark2.sql
    CREATE EXTERNAL TABLE <user_vpn>.docs2 (line STRING) stored as textfile location '/datasets/gutenberg-small/*.txt'

    %spark2.sql
    SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs2) w GROUP BY word ORDER BY word

## 4. Jupyter Notebooks

### Wordcount [wordcount-spark.ipynb](wordcount-spark.ipynb)

### Dataframes y preparación de datos en pyspark

* notebook: [Data_processing_using_PySpark.ipynb](Data_processing_using_PySpark.ipynb)
* datos ejemplo: [sample_data.csv](../../../datasets/spark/sample_data.csv)

### Manejo de notebooks en EMR

* varias opciones:

    ** a través del servicio jupyterhub como componente de EMR
    ** Notebooks como servicio de amazon para EMR (opción a trabajar)

Para trabajar con los notebooks gestionados por amazon, la gestión de paquetes, versión de python puede ser consultado en:

    https://docs.aws.amazon.com/emr/latest/ManagementGuide/emr-managed-notebooks-scoped-libraries.html

* Opciones importantes:

** cambiar versión de python:

    %%configure -f
    { "conf":{
    "spark.pyspark.python": "python3",
    "spark.pyspark.virtualenv.enabled": "true",
    "spark.pyspark.virtualenv.type":"native",
    "spark.pyspark.virtualenv.bin.path":"/usr/bin/virtualenv"
    }}

** Example – Listing Current Libraries

    sc.list_packages()

** Example – Installing the NLTK Library

    sc.install_pypi_package("nltk")

** Example – Uninstalling a Library

    sc.uninstall_package("nltk")

====

# Universidad EAFIT # Curso ST0263 Tópicos Especiales en Telemática

# LAB  de SPARK

## Realizar las siguientes actividades y adjuntar las evidencias:

### 1. ejecutar todos los notebooks que se encuentran en el directorio
'03-spark' tanto en AWS EMR Jupyerhub como en Google Colab.

Notebooks:

* ejecutar: `Data_processing_using_PySpark.ipynb` en los siquientes ambientes:

** EMR con datos en AWS S3

** Google Colab con datos en Google Drive

** Google Colab con datos en AWS S3

* ejecutar: `spark_colab_ejercicios` en los siguientes ambientes:

** EMR con datos en AWS S3

** Google Colab con datos en Google Drive

* ejecutar: `wordcount-spark.ipynb` (en EMR) y `wordcount-spark-colab.ipynb` (en
  Google Colab)

* ejercutar: `wc-pyspark.py` en el nodo master de EMR

### Como evidencia de la ejecución de estos notebooks, deberá adjuntar los
notebooks con los resultados de la ejecución celda por celda en cada uno de los
ambientes solicitados, o en caso de no poderse -> tomar pantallazos.

### 2. BASADO EN LOS DATOS DE COVID19, realice procesamiento básico a nivel de
dataframe y  cálculos de estadistica básica descriptiva y procesamiento de
datos categoricos (groupBy + Operación) basados en las siguientes preguntas de
negocio:

#### Procesamiento Básico a nivel de dataframes:


* carga de datos csv en spark desde un bucket S3 (desde EMR con Notebooks
  administrados y con el servicio jupyterhub, desde Google Colab y se deja
opcionalmente desde boto3)

* borrar y crear algunas columnas

* realizar filtrados de datos por alguna información que le parezca interesante

* realizar alguna agrupación y consulta de datos categorica, por ejemplo número
  de casos por región o por sexo/genero.

* finalmente salve los resultados en un bucket público en S3

* realice toda la documentación en el mismo notebook.

Los datos los van a obtener de:

* https://www.datos.gov.co/Salud-y-Protecci-n-Social/Casos-positivos-de-COVID-19-en-Colombia/gt2j-8ykr/data

o en los [datasets](../../../datasets) hay datos ejemplo de covid19 para colombia.

#### Responder a las siguientes preguntas de negocio tanto con procesamiento
con Dataframes como en SparkSQL:

Ver ejemplo de procesamiento en SparkSQL en:
https://www.oreilly.com/library/view/learning-spark-2nd/9781492050032/ch04.html

* Los 10 departamentos con más casos de covid en Colombia ordenados de mayor a
  menor.
* Las 10 ciudades con más casos de covid en Colombia ordenados de mayor a
  menor.
* Los 10 días con más casos de covid en Colombia ordenados de mayor a menor.
* Distribución de casos por edades de covid en Colombia.
* Realice la pregunda de negocio que quiera sobre los datos y respondala con la
  correspondiente programación en spark.

Reiteración: estas preguntas de negocio deben ser resueltas tanto en Dataframes
como en SQL con Spark.

Requerimiento, los datos de salida de estas 5 preguntas de negocio, deben ser
salvadas en archivos tanto en formato .csv como .parquet.

Los datos deben ser salvados en S3 y en Google Drive segun sea el ambiente de
ejecución.

Debe probarlos en ambos ambientes de ejecución y adjuntar como evidencia los
notebooks en colab y emr, con los resultados en cada celda ejecutada.
