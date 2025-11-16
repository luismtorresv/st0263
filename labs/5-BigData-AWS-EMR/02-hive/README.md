# Universidad EAFIT
# Curso ST0263 Tópicos Especiales en Telemática

# LAB 3-2: PROCESAMIENTO SQL CON APACHE HIVE

## TABLAS SENCILLAS EN HIVE

## 1 Conexión al cluster Hadoop via HUE en Amazon EMR

Hue Web (cada uno tiene su propio cluster EMR)

    http://ec2.compute-1.amazonaws.com:8888


Usuarios: (entrar como hadoop/*********)

    username: hadoop
    password: ********

## 2. Los archivos de trabajo hdi-data.csv y export-data.csv

```
/user/hadoop/datasets/onu
```

## 3. Gestión (DDL) y Consultas (DQL)

### cada uno deberá crear su propia BD:

    CREATE DATABASE usernamedb

### Crear la tabla HDI en Hive:
```
# tabla manejada por hive: /user/hive/warehouse
use usernamedb;
CREATE TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE

# se requiere cargar datos a la tabla asi:
#
# copiando datos directamente hacia hdfs:///warehouse/tablespace/managed/hive/mydb.db/hdi

$ hdfs dfs -cp hdfs:///user/hadoop/datasets/onu/hdi-data.csv hdfs:///warehouse/tablespace/managed/hive/hadoopdb.db/hdi

#
# cargardo datos desde hive:

## darle primero permisos completos al directorio:
## hdfs dfs -chmod -R 777 /user/hadoop/datasets/onu/

$ load data inpath '/user/hadoop/datasets/onu/hdi-data.csv' into table HDI

# tabla externa en hdfs:
use usernamedb;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/datasets/onu2/hdi/'

# tabla externa en S3:
use usernamedb;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://username_datalake/datasets/onu2/hdi/'

```

Nota: Esta tabla la crea en una BASE DE DATOS 'mydb'
```
use usernamedb;
show tables;
describe hdi;
```

### hacer consultas y cálculos sobre la tabla HDI:
```
select * from hdi;

select country, gni from hdi where gni > 2000;
```

### EJECUTAR UN JOIN CON HIVE:

### Obtener los datos base: export-data.csv

usar los datos en 'datasets' de este repositorio.

### Iniciar hive y crear la tabla EXPO:

```
use usernamedb;
CREATE EXTERNAL TABLE EXPO (country STRING, expct FLOAT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://username_datalake/datasets/onu2/export/'
```

### EJECUTAR EL JOIN DE 2 TABLAS:
```
SELECT h.country, gni, expct FROM HDI h JOIN EXPO e ON (h.country = e.country) WHERE gni > 2000;
```

## 4. WORDCOUNT EN HIVE:
```
use usernamedb;
CREATE EXTERNAL TABLE docs (line STRING)
STORED AS TEXTFILE
LOCATION 'hdfs://localhost/user/hadoop/datasets/gutenberg-small/';

--- alternativa2:
CREATE EXTERNAL TABLE docs (line STRING)
STORED AS TEXTFILE
LOCATION 's3://username_datalake/datasets/gutenberg-small/';
```

// ordenado por palabra
```
SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs) w
GROUP BY word
ORDER BY word DESC LIMIT 10;
```
// ordenado por frecuencia de menor a mayor
```
SELECT word, count(1) AS count FROM (SELECT explode(split(line,' ')) AS word FROM docs) w
GROUP BY word
ORDER BY count DESC LIMIT 10;
```

### RETO:

¿cómo llenar una tabla con los resultados de un Query? por ejemplo, como almacenar en una tabla el diccionario de frecuencia de palabras en el wordcount?
