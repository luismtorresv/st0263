# Laboratorio 3-3: Procesamiento SQL con Apache Hive

## Tablas sencillas en Hive

Los archivos de trabajo del dataset de la `onu`:
- `hdi-data.csv`
- `export-data.csv`

Estarán guardados en:

```shell
/user/hadoop/datasets/onu
```

## Gestión (DDL) y Consultas (DQL)

### Crear una base de datos

```sql
CREATE DATABASE ${username}db;
```

### Crear la tabla HDI manejada por Hive

```sql
use ${username}db;


CREATE TABLE HDI (
    id INT,
    country STRING,
    hdi FLOAT,
    lifeex INT,
    mysch INT,
    eysch INT,
    gni INT
)

ROW FORMAT DELIMITED FIELDS
TERMINATED BY ','
STORED AS TEXTFILE
TBLPROPERTIES ("skip.header.line.count"="1");
```

### Cargar datos a la tabla

Podemos cargarlos de varias formas. Acá se describen dos formas:

1. Copiar directamente el archivo usando el comando `hdfs`
2. Usando Apache Hive para cargarlos.


#### Cargando con `hdfs`

```shell
hdfs dfs -cp \
  /user/hadoop/datasets/onu/hdi-data.csv \
  /user/hive/warehouse/${DB_NAME}.db/hdi

hdfs dfs -ls /user/hive/warehouse/${DB_NAME}.db/hdi
```

### Cargando datos con Hive

1. Darle permisos completos al directorio con:

   ```shell
   hdfs dfs -chmod -R 777 /user/hadoop/datasets/onu/
   ```

2. Cargar los datos:

   ```sql
   LOAD DATA inpath '/user/hadoop/datasets/onu/hdi-data.csv'
   INTO TABLE hdi
   ```

### Tabla externa con HDFS

```sql
USE ${username}db;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION '/user/hadoop/datasets/onu2/hdi/'
```

### Tabla externa con S3

```sql
use ${username}db;
CREATE EXTERNAL TABLE HDI (id INT, country STRING, hdi FLOAT, lifeex INT, mysch INT, eysch INT, gni INT)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
STORED AS TEXTFILE
LOCATION 's3://${bucket-name}/datasets/onu2/hdi/'
```

> [!NOTE]
>
> Esta tabla la crea en una base de datos `mydb`.

```sql
USE ${username}db;
SHOW TABLES;
DESCRIBE hdi;
```

## Consultas

Hacer consultas y cálculos sobre la tabla `hdi`:

### Ejemplos

#### Listar todo

```sql
SELECT
    *
FROM
    hdi;
```

#### Filtrar por `gni`

```sql
SELECT
    country,
    gni
FROM
    hdi
WHERE
    gni > 2000;
```

### Ejecutar un `JOIN` con Hive

- Obtener los datos base: `export-data.csv`
- Usar los datos en [`datasets`](../../../datasets/) de este repositorio.
- Iniciar Hive y crear la tabla `EXPO`

    ```sql
    use ${username}db;
    CREATE EXTERNAL TABLE EXPO (country STRING, expct FLOAT)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ','
    STORED AS TEXTFILE
    LOCATION 's3://{bucket-name}/datasets/onu2/export/'
    ```
- Ejecutar el `JOIN` de dos tablas

  ```sql
  SELECT h.country, gni, expct
  FROM HDI h
  JOIN EXPO e
  ON (h.country = e.country)
  WHERE gni > 2000;
  ```

## Word count

### Creación de la tabla

```sql
use ${username}db;
```

#### Si es con HDFS

```sql
CREATE EXTERNAL TABLE docs (line STRING)
STORED AS TEXTFILE
LOCATION 'hdfs://localhost/user/hadoop/datasets/gutenberg-small/';
```

#### Si es con el bucket S3

```sql
CREATE EXTERNAL TABLE docs (line STRING)
STORED AS TEXTFILE
LOCATION 's3://${bucket-name}/datasets/gutenberg-small/';
```

### Consultas

#### Ordenado alfabéticamente

```sql
SELECT word, count(1)
AS count
FROM (
    SELECT explode(split(line,' '))
    AS word FROM docs
    ) w
GROUP BY word
ORDER BY word DESC LIMIT 10;
```

#### Ordenado por frecuencia de menor a mayor

```sql
SELECT word, count(1)
AS count
FROM (
    SELECT explode(split(line,' '))
    AS word FROM docs
    ) w
GROUP BY word
ORDER BY count DESC LIMIT 10;
```

### Reto adicional

¿Cómo llenar una tabla con los resultados de un query? Por ejemplo, ¿cómo
almacenar en una tabla el diccionario de frecuencia de palabras en el word
count?
