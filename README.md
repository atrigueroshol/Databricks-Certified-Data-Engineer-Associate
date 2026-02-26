# Databricks Certified Data Engineer Associate

Databricks es una plataforma multicloud para data lakehouse basada en Apache Spark.

Un **data lakehouse** es una plataforma que unifica las ventajas de un datalake y un datawarehouse.

<p align="center">
  <img src="https://github.com/atrigueroshol/Databricks-Certified-Data-Engineer-Associate/blob/main/lakehouse.drawio.png?raw=true" alt="Texto alternativo">
</p>

## Arquitectura
La arquitectura de la plataforma de **Databricks** es la siguiente:

1.  **Cloud Service**  
    Databricks es una plataforma **multicloud** y puede desplegarse sobre **Amazon Web Services (AWS)**, **Microsoft Azure** y **Google Cloud**.  
    El proveedor cloud se encarga de la **infraestructura**, como máquinas virtuales, redes, almacenamiento y la creación de **clusters**.
    
2.  **Runtime**  
    El Databricks Runtime está basado en **Apache Spark** e integra **Delta Lake**, que proporciona transacciones ACID, control de versiones y fiabilidad sobre el data lake.
    
3.  **Workspace**  
    El Workspace es la **interfaz gráfica** de Databricks que permite realizar tareas de **Data Engineering, Data Warehousing (SQL/BI) y Machine Learning**, mediante notebooks, jobs, dashboards y herramientas colaborativas.
    
<p align="center">
  <img src="https://github.com/atrigueroshol/Databricks-Certified-Data-Engineer-Associate/blob/main/arch.drawio.png?raw=true" alt="Texto alternativo">
</p>

## Notebooks
Los nootebooks por defecto tienen python como lenguaje pero se puede modificar. Además se pueden combinar varios lenguajes en un mismo nootebook añadiendo % al principio de una celda.
```
%python   → Ejecuta código Python (por defecto)
%sql      → Ejecuta consultas SQL (Spark SQL)
%scala    → Ejecuta código Scala
%r        → Ejecuta código R
%md       → Celdas de documentación en Markdown
%run      → permite ejecutar otro nootebook
%fs       → permite interactuar con el sistema de archivos
```
Además Datrabricks proporciona **dbutils** que permite interactuar con el entorno del workspace y del cluster, facilitando tareas de administración, exploración de datos y gestión de pipelines desde los notebooks.
### Explorador de Variables
El Variable Explorer permite ver de forma rápida todas las variables definidas en una sesión del notebook, mostrando su nombre, tipo de dato y valor actual.  Además, para DataFrames de Spark y Pandas, muestra dimensiones, nombres de columnas y permite consultar el esquema completo al pasar el cursor.
### Python Debugger
Databricks Notebooks ahora permiten depuración interactiva para inspeccionar la ejecución de código Python en tiempo real. Puedes definir breakpoints directamente en el notebook para pausar la ejecución y revisar valores de variables, lo que facilita detectar errores de lógica.

Para iniciar el depurador en Databricks:
1.  Añade uno o más breakpoints haciendo clic en el margen de una celda.
2.  Haz clic en “Debug cell”.
3.  Se inicia una sesión de depuración donde puedes avanzar paso a paso usando la barra de herramientas.

#### Versiones
Databricks permite ver las anteriores versiones de un nootebook y restaurarlas. Unicamente tenemos versiones pero no tenemos ramas de versionado.

## Carpetas Git

Se puede conectar una plataforma de versionado como github con Databricks. Una vez conectado nuestro repositorio podemos crear nuevas ramas, commitear, pushear y hacer pull. Para mergear ramas se debe hacer desde la plataforma de versionado como github.

## DeltaLake
DeltaLake es un framework de código abierto que añade transacciones ACID, control de versiones y fiabilidad a los data lakes.
Es un componente que esta desplegado en el cluster como parte del runtime. Cuando creamos una tabla delta se almacena en el almacenamiento en varios ficheros de datos de tipo parquet y delta logs en formato JSON.

 -   **Data Files**  
    Los datos se almacenan en **ficheros Parquet**. Cada vez que se realiza una operación de **escritura, actualización o borrado**, se crean **nuevos ficheros Parquet** con los datos actualizados y se **marcan como obsoletos** los ficheros anteriores (no se eliminan inmediatamente).
    
-   **Delta Log**  
    El _Delta Log_ guarda un **registro de todas las transacciones** realizadas sobre la tabla y actúa como la **fuente de verdad** de su estado.  
    Cada fichero **JSON** contiene información sobre la operación realizada (add, remove, metadata, etc.) y los **ficheros de datos afectados**

### Crear Tablas
```sql
CREATE TABLE users(
id INTEGER,
name STRING,
surname STRING,
age INTEGER
)
```
### Insertar Datos
```sql
INSERT INTO  users (id, name, surname, age) VALUES
(1, 'Ana', 'Gómez', 28),
(2, 'Luis', 'Martínez', 35),
(3, 'Carla', 'Rodríguez', 22),
(4, 'Javier', 'Pérez', 40),
(5, 'Sofía', 'López', 30);
```
### Descripcción de la tabla
La operación **DESCRIBE DETAIL** devuelve metadatos completos de la tabla: 
```sql
DESCRIBE DETAIL  users
```
Ejemplo.
```
format: delta
name: users
location: dbfs:/user/hive/warehouse/users
numFiles: 5
sizeInBytes: 20480
partitionColumns: []
```
### Historial de la tabla
La operación **DESCRIBE HISTORY** devuelve el historial de cambios de una tabla:
```sql
DESCRIBE HISTORY users
```
Ejemplo.
```
version | timestamp           | operation | operationMetrics
-------------------------------------------------------------
5       | 2026-02-18 10:15:30 | WRITE     | {numOutputRows=5}
4       | 2026-02-17 18:02:11 | DELETE    | {numDeletedRows=2}
3       | 2026-02-16 09:40:00 | MERGE     | {numTargetRowsUpdated=3}

```
### Consultar tabla y versiones Antiguas
En databricks se puede consultar una tabla y versiones anteriores.
```sql
-- VERSION ACTUAL
SELECT * FROM users;
--VERSION ANTIGUA
SELECT * FROM  users VERSION AS OF 1;
SELECT  *  FROM  users@v1;
```
Para saber el número de la versión que queremos consultar podemos utilizar DESCRIBE HISTORY.
### Restaurar una version antigua
En databricks podemos restaurar una versión antigua de la tabla con **RESTORE TABLE**.
```sql
RESTORE TABLE users TO VERSION AS OF 1;
```
### Compactar ficheros
Con **OPTIMIZE** podemos optimizar el rendimiento de las consultas reorganizando los ficheros de datos. Como ya sabemos cada operación sobre una tabla crea un archivo y con OPTIMIZE unifica esos archivos pequeños en archivos más grandes.
```sql
OPTIMIZE users;
```
### Indexación de ficheros
Para la indexación de nuestos datos debemos utilizar la operación **ZORDER** que reorganiza los datos de los ficheros para mejorar el rendimiento de las consultas. 
```sql
OPTIMIZE users
ZORDER BY (surname, age);
```
### Limpieza de ficheros
Con la operación **VACUUM** eliminamos los ficheros de datos que ya no estan en uso. Debemos tener cuidado a la hora de ejecutar este comando ya que una vez hecho no podemos restaurar o consultar versiones anteriores de la tabla.
```sql
VACUUM users
```
### DATA FILE LAYOUT 
Data file layout es la organización física de los ficheros que forman una tabla Delta. Optimizando la capa de data files se puede mejorar significativamente el tiempo de ejecución y el consumo de recursos** de las consultas.

Vamos a estudiar tres técnicas principales para optimizarla.

La primera técnica es el **partitioning**.  
Databricks crea una partición por cada valor distinto de la columna por la que se particiona, generando una carpeta por cada valor de la partición.

`CREATE TABLE users (
  id INTEGER,
  name STRING,
  surname STRING,
  age INTEGER ) USING DELTA
PARTITIONED BY (age);` 
 
El particionado puede mejorar mucho el rendimiento de las consultas cuando las tablas Delta son grandes, ya que permite pruning de particiones (solo se leen las carpetas necesarias).
Buenas prácticas
-   Particionar por columnas con baja cardinalidad
-   Usar columnas frecuentes en cláusulas `WHERE`
-   Evitar particionar por columnas con muchos valores distintos
    
Si se particiona por columnas de alta cardinalidad, se generan demasiadas carpetas y el rendimiento empeora.

Otra técnica es **Z-ORDER**, que reorganiza los datos dentro de los ficheros para mejorar el rendimiento de las consultas.

`OPTIMIZE users
ZORDER BY (surname, age);` 

-   Agrupa valores similares físicamente en los mismos archivos
-   Reduce la cantidad de datos leídos en filtros (`WHERE`) y joins
-   Complementa al particionado (no lo reemplaza)

Es efectivo cuando las columnas usadas en filtros o joins tienen media o alta cardinalidad y cuando las consultas combinan varias columnas.

Como desventajas tiene que cada ejecución de `OPTIMIZE ZORDER` reescribe archivos. Tras insertar nuevos datos, es necesario volver a ejecutar `OPTIMIZE`. Puede ser costoso a nivel de cómputo, por lo que no debe ejecutarse continuamente

La última técnica de optimización es **Liquid Clustering**, que consiste en una evolución del Z-order, ofreciendo mayor flexibilidad, mejor rendimiento y menor sobrecarga operativa.

A diferencia de los enfoques tradicionales Liquid Clustering no es compatible con PARTITIONING ni con ZORDER. El clustering se gestiona de forma dinámica, sin necesidad de reescribir completamente la tabla cada vez que cambian los patrones de acceso. Está especialmente optimizado para cargas de trabajo con consultas analíticas cambiantes.

Para activar o ejecutar el clustering, simplemente se utiliza el comando OPTIMIZE sobre la tabla. No es necesario especificar ZORDER BY.

Las claves de clustering pueden definirse de dos formas:
1.  Modo manual  
    Seleccionando las columnas más utilizadas en los filtros (WHERE) y joins de las consultas.
    
2.  Modo automático (recomendado)  
    Databricks analiza el historial de consultas y el acceso a los datos para elegir y ajustar automáticamente las claves de clustering más óptimas.
  
```sql
CREATE TABLE sales
(
  order_id STRING,
  customer_id STRING,
  country STRING,
  order_date DATE,
  amount DOUBLE
)
CLUSTER BY (country, order_date);
# CLUSTER BY AUTO
```
### Bases de datos en Databricks

En Databricks, una base de datos equivale conceptualmente a un schema en el Hive Metastore. Por esta razón, existen dos formas equivalentes de crear una base de datos:
```sql
CREATE DATABASE db_name; CREATE SCHEMA db_name; 
```
Ambos comandos son sinónimos y producen el mismo resultado.

Hive Metastore

Un schema en el Hive Metastore es un repositorio de metadatos que almacena información sobre:
-   Bases de datos (schemas)
-   Tablas   
-   Columnas   
-   Particiones
-   Ubicaciones de almacenamiento
    
Cada workspace de Databricks dispone de un almacenamiento asociado (por ejemplo, en cloud object storage) donde se mantiene el Hive Metastore.  
Por defecto, siempre existe un schema llamado:

`default` 

### Tipos de tablas (Managed y External)

En Databricks existen dos tipos principales de tablas, según cómo se gestione el almacenamiento de los datos.

1.  Managed Tables (tablas administradas)
 
La tabla se crea dentro del directorio del schema.

El metastore gestiona tanto los metadatos como los datos físicos.

Al ejecutar `DROP TABLE`, se eliminan la tabla y los datos del almacenamiento.

Ejemplo conceptual:

`CREATE TABLE sales_managed (
  id INT,
  amount DOUBLE );` 

Uso recomendado cuando Databricks es el único sistema que accede a los datos. Se desea una gestión automática del ciclo de vida
    
2.  External Tables (tablas externas)
3. 
La tabla se crea fuera del directorio del schema, indicando explícitamente la ubicación mediante `LOCATION`.

El metastore solo gestiona los metadatos, no los datos.

Al ejecutar `DROP TABLE`, solo se eliminan los metadatos; los datos permanecen intactos.

Ejemplo:

`CREATE TABLE sales_external (
  id INT,
  amount DOUBLE ) USING DELTA
LOCATION 'abfss://data@storageaccount.dfs.core.windows.net/sales/';`

### CTAS
Otra de las formas de crear una tabla es mediante el uso de CTAS (Create Table As Select Statement).
```sql
CREATE TABLE table_1 AS
	SELECT col_1, col_3 AS new_col_3 FROM table_2
```
La ventaja que tiene es que no se define el esquema manualmente si no que directamente lo infiere del SELECT. Además la tabla ya viene con los datos que obtiene de la consulta.

### Restricciones (Constraints)
Databricks soporta dos tipos de restricciones sobre las tablas:

 - NOT NULL constraints
```sql
#Creando la tabla
CREATE TABLE customers (
  customer_id STRING NOT NULL,
  name STRING,
  email STRING
);
#Sobre una tabla ya creada
ALTER TABLE customers ALTER COLUMN customer_id SET NOT NULL;
```
 - Check constraints
```sql
#Creando la tabla
CREATE TABLE orders (
  order_id STRING,
  amount DOUBLE,
  CONSTRAINT amount_positive CHECK (amount > 0)
);
#Sobre una tabla ya creada
ALTER TABLE orders ADD CONSTRAINT amount_positive CHECK (amount > 0);
```
Se debe tener en cuenta que si intentamos crear una restricción sobre una tabla con datos y alguno de las filas no cumple la restricción dará error al crear la restricción. Una vez creada la restricción si intentamos insertar una fila que no comple las condiciones la operación devolverá un error.

### Clonar Deltas
Existen dos formas de clonar las tablas delta en databricks. Esto nos puede servir para tener un backup o una copia de los datos para hacer pruebas.

 - Deep Clone: Copia los datos y los metadatos de la tabla. Para sincronizar cambios se debe ejecutar de nuevo el comando. Este comando no es eficiente cuando la cantidad de datos es muy grande.
```sql
CREATE TABLE table_clone DEEP CLONE source_table
```
 - Shallow Clone: Unicamente crea una copia de los delta transactions logs. Es muy útil para hacer pruebas sin correr el riesgo de modificar la tabla original.
 ```sql
CREATE TABLE table_clone SHALLOW CLONE source_table
```
### Vistas
Una vista es una tabla virtual que no tiene datos físicos., es una query de SQL que apunta a las tablas físicas. En databricks hay tres tipos de vistas. 

 - Stored Views: Son objetos persistentes en la base de datos.
```sql
CREATE VIEW view_name AS query
```
 - Temporary Views: Son objetos que solo persisten durante la sesion de spark. Una vez terminada la sesion se borran.
```sql
CREATE TEMP VIEW view_name AS query
```
 - Global Temporary Views: Son objetos vinculados al cluster. Mientras el cluster este levantado cualquier notebook puede acceder a la vista.
```sql
CREATE GLOBAL TEMP VIEW view_name AS query
```

## Procesamiento y transformaciones

### Consultas a ficheros
Para consultar datos de un archivo en Databricks debemos utilizar lo siguiente:
```sql
SELECT  *  FROM file_format.`path`
-- Ejemplos
SELECT * FROM csv.`/mnt/datos/ventas/ventas_2025.csv`
SELECT * FROM json.`/mnt/datos/clientes/clientes.json`
SELECT * FROM parquet.`/mnt/datos/ventas/ventas_2025.parquet`
SELECT * FROM delta.`/mnt/datos/ventas_delta`
```
Esta consulta funciona tanto para formatos self-describing (JSON, Parquet, ORC, Avro, Delta) como para formatos non self-describing (CSV, TSV, TXT).

En los formatos non self-describing es necesario proporcionar opciones adicionales (por ejemplo header, sep, inferSchema o un esquema explícito).

En el path se puede indicar un fichero concreto, un patrón de ficheros o un directorio completo.

Si queremos obtener los datos como strings de ficheros de tipo texto (JSON, CSV, TSV y TXT) podemos utilizar:
```sql
SELECT  *  FROM text.`path`
```
Si queremos obtener los datos como bytes de ficheros de tipo imagen podemos utilizar:
```sql
SELECT  *  FROM binaryFile.`/path/to/file`
```
Normalmente, cuando extraemos datos de ficheros externos queremos cargarlos en nuestro lakehouse y, para ello, podemos usar CTAS:
```sql
CREATE  TABLE table_name AS  SELECT  *  FROM file_format.`path`
```
Cuando usamos CTAS para la creación de tablas no se puede definir el esquema de la tabla ni se soportan opciones del fichero, lo que puede suponer un problema. Por ello existe esta otra opción:
```sql
CREATE  TABLE table_name  
 (col_name1 col_type1, ...)  
USING data_source  
OPTIONS (key1 = val1, key2 = val2, ...)  
LOCATION path
```
De esta forma siempre estaremos creando una tabla externa que referencia ficheros almacenados externamente y, por lo tanto, no es una tabla de tipo Delta y pierde las ventajas asociadas a este tipo de tablas. Para solventar esto, una solución consiste en crear una vista temporal y, posteriormente, usar CTAS para crear una tabla a partir de dicha vista.

En la última versión de Databricks se ha introducido una función llamada `read_files`, que facilita el proceso de creación de tablas a partir de ficheros.

### Operaciones de escritura y sobreescritura
Existen varias formas de sobreescribir datos en una tabla. Hay ventajas en sobreescribir una tabla en vez de borrarla y crear una nueva tabla. Por ejemplo la version antigua de la tabla sigue existiendo y podemos volver a ella. Además sobreescribir una tabla es más rápido ya que no necesita borrar ningún fichero. Además es una operación que se hace en paralelo y se puede seguir consultando la tabla mientras el proceso termina de sobreescribir.

El primer metodo para reescribir una tabla es usando **CREATE OR REPLACE** que remplaza todo el contenido de la tabla.
```sql
CREATE OR REPLACE TABLE name AS
SELECT * FROM format.`path`
```
El segundo metodo para sobreescribir en una tabla es usando **INSERT OVERWRITE**. Los datos de la tabla serán reemplazados por los datos de la query. Esta secuencia solo puede sobreescribir en una tabla pero no puede crearla y por lo tanto solo puede escribir datos que coincidan con el esquema de la tabla o si no recibiremos una excepción al ejecutar la operación.
```sql
INSERT OVERWRITE name
SELECT * FROM format.`path`
```
Si queremos insertar datos en una tabla podemos usar INSERT INTO. Si ejecutamos la orden varias veces tenremos registros duplicados. 
```sql
INSERT INTO name
SELECT * FROM format.`path`
```
Si queremos evitar tener registros duplicados podemos usar MERGE.
```sql
CREATE OR REPLACE TEMP VIEW name_view AS
SELECT * FROM format.`path`

MERGE INTO name_table A
USING name_view B
ON a.key = b.key
WHEN MATCHED
	UPDATE
WHEN NOT MATCHED THEN INSERT *
```

### Transformaciones Avanzadas

Databricks permite tener columnas en formato JSON de tipo STRING. Por ejemplo la columna cliente podría ser:
```json
{"name": "Alberto", last_name: "Trigueros", "gender":male"}
```
```sql
SELECT client:name
FROM purchases
```
#### JSON TO FLAT COLUMNS
La función **from_json** permite convertir JSON en STRUCT. La función from_json necesita un atributo **shema_of_json**. Una vez que hemos hecho el from_json podemos acceder a la columnas de la siguiente forma:
```sql
SELECT from_json(client, schema_of_json('{"name": "Alberto", last_name: "Trigueros", "gender":male"}')) AS client_struct
SELECT * FROM client_struct.name
```
Una vez creado el struct podemos utilizar una vista para flatear las columnas.

#### EXPLODE
En databricks existe una función llamada **explode()** que permite flatear los arrays creaundo una fila por cada valor del array.
```sql
SELECT explode(column) AS column FROM table
```
#### COLLECT_SET
La operación **collect_set** es una función de agregación que permite obtener valores unicos por la agrupación. Ejemplo.
```sql
SELECT  
user_id,  
collect_set(product) AS productos_unicos  
FROM ventas  
GROUP  BY user_id;
```
Output:
| user_id | productos_unicos |
|--------:|------------------|
| 1       | ["A", "B"]       |
| 2       | ["C"]            |

### UDF
Para crear una funcion en Databricks utilizaremos la siguiente estructura
```sql
CREATE FUNCTION name(field TYPE)
RETURNS type
RETURN logic
```
ejemplo:
```sql
CREATE OR REPLACE FUNCTION to_user_label(name STRING)
RETURNS STRING
RETURN CONCAT('USER_', UPPER(name));
```
## Desarrollo e Ingesta

En Databricks para el procesamiento en **streaming** de datos existe **Spark Structured Streaming** que es un motor de procesamiento en streaming. Este motor permite consultar un datasource infinito y volcar los resultados en tablas o en ficheros.

### ReadStream
Operación utilizada para definir una fuente de datos infinita. No es una acción por lo que no se ejecuta directamente.
```python
df = spark.readStream.table("name_table)
```
Una vez creado el df se pueden definir transformaciones y operaciones sobre el dataframe. Se debe tener en cuenta que en estos casos hay operaciones que no soportan los df en streaming como sorting y deduplicación.

### WriteStream
Operación que define el destino del procesamiento y el modo de ejecución del stream.
```python
df.writeStream
	.trigger(option)
	.outputMode(option)
	.option(option)
	.table(option)
```
trigger:
 - Defecto: por defecto se procesa cada 500ms
```python
.trigger()
```
 - Intervalo fijo: Procesa los datos en micro batch
```python
.trigger(processingTime="5 minutes")
```
 - Batch: Procesa todos los datos de una vez y luego para.
```python
.trigger(once=true)
```
 - Micro-batches: Porcesa todos los datos disponible en microbatches y luego para
```pythonç
trigger(availableNow=true)
```
outputMode:
 - append: se escriben solo nuevas filas 
 - complete: se reescribe la tabla entera
 
option(checkpointLocation, path): Sirve para saber que datos ya se han procesado y recuperarse tras fallos.

### Ingesta incremental desde ficheros

Necesitamos recibir data de nuevos ficheros desde la última ingesta de datos. No queremos reprocesar los ficheros ya procesados, solo queremos procesar los nuevos ficheros. 
Hay dos métodos COPY INTO y Auto Loader.

#### COPY INTO
Es un comando en SQL que permite cargar datos de un directorio a una tabla. Cada vez que se ejecuta el comando solo carga los datos de los nuevos ficheros.
```sql
COPY INTO my_table
FROM 'path'
FILEFORMAT = format
FORMAT_OPTIONS(options)
COPY_OPTIONS(options)
```
#### Auto Loader
Usa Spark Structured Streaming. Sirve para procesar una gran cantidad de datos que se encuentre en un directorio. Tiene todas las ventajas de Spark Structured Streaming. Ejemplo:
```python
spark.readStream
	.format("cloudFiles")
	. option("cloudFiles.format", source_format)
	. writeStream
		.option("checkpointLocation", directorio)
		.table(table_name)
```
#### COPY INTO VS Auto Loader

| COPY INTO| Auto Loader|
|--------:|------------------|
| Miles de filas       | Millones de filas       |
|    Escalado poco eficiente    | Escalado Eficiente     


### Arquitectura Medallion
Esta arquitectura organiza los datos en capas. Esta arquitectura esta centrada en mejorar la estructura y la calidad de los datos según avanzan los datos por las capas.  
<p align="center">
  <img src="https://github.com/atrigueroshol/Databricks-Certified-Data-Engineer-Associate/blob/main/medallion.drawio.png?raw=true" alt="Texto alternativo">
</p>


 

