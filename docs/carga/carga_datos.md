##Primera Carga

###Cargar los datos en Mysql

####1. Creación y carga de estructura y datos comunes
Para comenzar es necesario iniciliazar la base de datos del cliente mediante los scripts de estructura y datos comunes.
Estos se pueden encontrar en el repositorio de [**Subversion**](https://iccacdb.dynalias.com:8443/svn/RLT_MESSAGES/Documentacion/03.%20Dise%c3%b1o/01.%20Modelo/03.%20Modelo%20Relacional/scripts)


####2. Carga a partir de los ficheros.
Los ficheros, uno por tabla, que siguen el formato definido en ETL `estandar.xlsx`, se colocarán en:

	/RM/etl

Ejecutar el jar creado con JasperSoft llamado (nombre por ej:  **cargaHistoricoETLMySql.jar**


####3. Carga de imágenes:
Si vienen con el formato `<id_producto.extension>`
 * Subir por ftp a `/var/www/html/shared/clientes/{id_cliente}/imagenesProducto`
 * Actualizar en base de datos las imágenes de los productos. Para ello se ejecuta con la consola de Symfony el siguiente comando:


```bash
cd var/www/html/current
php app/console producto:actualiza:imagenes <id_cliente>
```

Este comando lee las imágenes de productos que hay en el directorio de cliente y las asigna al producto correspondiente. Para ello, el nombre de la imagen tiene que tener un formato específico `<id_producto.extension>` **Ej: 157.jpg**

####4. Activcación de `Productos` y calculo de `Gamas`
Por defecto, todos los productos vienen desactivados. Paractivarlos tan solo hay que poner el campo `activo` a 1 en base de datos.

```sql
-- como por defecto desactivados:
UPDATE producto SET activo=1
```

Para calcular la gama de los productos tan solo hay que ejecutar la siguiente sentencia:

```sql
UPDATE 
  producto AS prod 
  INNER JOIN 
    (SELECT 
      p.id_producto,
      (p.precio_unitario * 100) / tab_media.med AS gama 
    FROM
      producto p,
      (SELECT 
        id_categoria,
        AVG(precio_unitario) med 
      FROM
        producto 
      GROUP BY id_categoria) tab_media 
    WHERE p.id_categoria = tab_media.id_categoria) AS aux 
    ON prod.id_producto = aux.id_producto SET prod.gama = aux.gama 
```

###Carga de datos en Hive (Hadoop)
####1. Inicializar la base de datos del cliente en Hive
Para comenzar el proceso de carga es necesario inicializar la base de datos del cliente en Hive. Para ello ejecutamos el script:

```bash
cd /opt/cloudera/scripts/hive
./hive_script.sh init -d c{id_cliente}
```

Este comando compruab si existe el fichero de parámetros para el cliente indicado en la carpeta `/parameters`. En caso de que no lo encuentre se le preguntará por los datos de conexión de la base de datos del cliente indicado.

```bash
El archivo de configuracion parameters/c6.parameters no existe. Por favor introduzca los siguientes datos para crearlo.
Introduzca la IP del servidor de base de datos:
192.168.100.82
Introduzca el puerto:
3306
Introduzca nombre de usuario:
admin
Introduzca contraseña:
icca
Fichero de configuración creado correctamente
```

Con esto se habrán creado la base de datos y todas las tablas necesarias para realizar la importación de los datos.

**WARNING:** Este comando borra, si existían, todas las tablas de la base de datos con el mismo nombre.

####2. Importación de los datos
Para realizar la importación de los datos del cliente tan solo es necesario ejecutar el comando:

```bash
./hive_scripts.sh import -d c{id_cliente}
```

Este comando importará las tablas `cliente`, `categoria` y `producto`.

Para importar la tabla `compra` es necesario indicar explicitamente la ruta del fichero de compras (ya sea total o incremental).


```bash
./hive_scripts.sh import -d c{id_cliente} -t compra --csv /path/fichero/compras.txt
```

####3. Cálculos de Insight


