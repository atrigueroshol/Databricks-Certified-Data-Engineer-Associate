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
```sql
DESCRIBE  DETAIL  users
```
### Consultar versiones Antiguas
```sql

```
### Compactar ficheros
```sql

```
### Indexación de ficheros
```sql

```
### Limpieza de ficheros
```sql

```

