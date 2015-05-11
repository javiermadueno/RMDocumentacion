##Hive

###Importacion de tablas en Hive
####Creacion de una tabla
La importación de tablas se puede realizar desde un simple archivo de texto hasta con una acción directa de MySQL.
Primera hay que crear la tabla para almacenar la información.

```bash
CREATE TABLE compras (year STRING, temperature INT, quality INT)
ROW FORMAT DELIMITED
FIELDS TERMINATED BY '\t';
```

La primera linea crea la tabla con la estructura y el tipo de datos especificados.
La segunda linea, indica que cada columna de cada fila esta delimitados por un ```tab```. 

Para realizar las consultas de manera mas eficiente es conveniente organizar la tabla en Hive en particiones basadas en columnas de la tabla.

En nuestro caso debemos particionar la tabla ```compra``` por ```fecha``` y ```cliente```.

```bash
create table compra (id_compra BIGINT, id_producto BIGINT,id_cliente  BIGINT,  fecha TIMESTAMP, id_tipo int, id_pos int, cantidad_pagada DOUBLE,unidades int, cod_promocion STRING, id_ticket string, codigo_vale STRING, personalizado1 STRING, personalizado2 STRING, personalizado3 STRING)
clustered by (id_cliente) sorted by (id_cliente asc) into 8 buckets;

```



####Importacion de datos
#####Importaciones incrementales con Sqoop
Para tener sincronizada la tabla de compras con Hive es necesario realizar importaciones incrementales utilizando las opciones ```--check-column``` y ```--last-value```.

El valor especificado para ```--last-value``` puede ser un ID que se incremente automaticamente en la base de datos, como una ```AUTOINCREMENT``` primary key en MySQL. Este modo es perfecto para el caso en el que se añadan nuevas filas, pero las filas existentes no se actualicen. Este modo se denomina ```append``` y se activa mediante la opcion ```--incremental append```.
AL finalizar una importacion incremental, Sqoop mostrará el valor que ha de ser especificiado en  ```--last-value``` en la próxima importación. Esto es util para hacer imprtaciones incrementales manualmente, pero no para ejecutar importaciones periodicas, para lo que es mejor utilizar los ```Sqoop jobs``` que automaticamente almacenan el último valor para la próxima ejecución.

Ejemplo:

```bash
$ sqoop job --create importar_compras  -- import \
--connect jdbc:mysql://192.168.100.229:3306/3 \
--table compra -m 2 \
--username=admin --password=icca \
--hive-import --hive-table c3.compra  \
--warehouse-dir=/user/hive/3 \
--incremental append \
--check-column id_compra \
--last-value 0 \
--split-by id_cliente
```

Para ejectutar el job tan solo hay que invocarlo de la siguiente manera:

```bash
$ sqoop job --exec importar_compras
```






