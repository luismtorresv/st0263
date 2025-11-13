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

o en los [datasets](datasets) hay datos ejemplo de covid19 para colombia.

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

