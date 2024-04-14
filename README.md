# Herramientas_de_BigData
Entorno Docker con Hadoop, Spark y Hive

Implementar y ejecutar cada script por separado el tercer script va ir cambiando conforme cambiemos de formato.

    git clone https://github.com/lopezdar222/herramientas_big_data
    cd herramientas_big_data
    sudo docker-compose -f docker-compose-vX.yml up -d

1) HDFS

       sudo docker-compose -f docker-compose-v1.yml up -d
   
Creamos el contenedor llamado (namenode) y entramos.
        
        sudo docker exec -it namenode bash
        cd home 

Crearemos el directorio Datasets y salimos
        
        mkdir Datasets
        exit
Cargamos los archivos en la carpera Datasets, dentro del contenedor "namenode".

        sudo docker cp <path><archivo> namenode:/home/Datasets/<archivo>

Ejecutar el archivo 'Paso00.sh'.Pero para poder ejecutarlo le damos permiso de ejecucion

        chmod u+x Paso00.sh
        
Crear un directorio en HDFS llamado "/data".
        
        hdfs dfs -mkdir -p /data

Ubicarse en el contenedor "namenode".

        sudo docker exec -it namenode bash

Crear un directorio en HDFS llamado "/data".

        hdfs dfs -mkdir -p /data

Copiar los archivos csv provistos a HDFS:

      hdfs dfs -put /home/Datasets/* /data

Este proceso de creación de la carpeta data y copiado de los arhivos, debe poder ejecutarse desde un shell script.

Nota: Busque dfs.blocksize y dfs.replication en http://<IP_Anfitrion>:9870/conf para encontrar los valores de tamaño de bloque y factor de réplica respectivamente entre otras configuraciones del sistema Hadoop.

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/596a9f3f-0322-4eab-a5b8-71ff7263a386)

2) Hive
   
        sudo docker-compose -f docker-compose-v2.yml up -d
   
El comando copia el archivo 'Paso02.hql' desde tu sistema de archivos local al directorio '/opt/' dentro del contenedor llamado "hive-server".

         sudo docker cp ./Paso02.hql hive-server:/opt/

         
 ubicarse dentro del contenedor, acceder al archivo 'Paso02.hql' y salir

      sudo docker exec -it hive-server bash
       hive -f Paso02.hql
       exit



Para comprobar que cargo correctamente la base de datos entramos a hive y ejecutamos una query.

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/254ed713-c805-4fca-bbc9-ac073f1f5f04)

3) Formatos de Almacenamiento

       sudo docker-compose -f docker-compose-v2.yml up -d
El comando copia el archivo 'Paso03.hql' desde tu sistema de archivos local al directorio '/opt/' dentro del contenedor llamado "hive-server".
        
        sudo docker cp ./Paso03.hql hive-server:/opt/

ubicarse dentro del contenedor, acceder al archivo 'Paso03.hql'  en este lugar es donde se realizan los pruebas hay que tener paciencia para que se carge el código.Si accedes a la hive en este lugar puedes realizar las consultas y para salir usas con el comando ('exit;' o 'quit')

      sudo docker exec -it hive-server bash
       hive -f Paso03.hql
       exit

![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/137fdd60-9f2c-433b-b6d3-65ea8083e65a)

4) SQL
La mejora en la velocidad de consulta que puede proporcionar un índice tiene el costo del procesamiento adicional para crear el índice y el espacio en disco para almacenar las referencias del índice. Se recomienda que los índices se basen en las columnas que utiliza en las condiciones de filtrado. El índice en la tabla puede degradar su rendimiento en caso de que no los esté utilizando. Crear índices en alguna de las tablas cargadas y probar los resultados:

Dentro del hive realizamos diferentes consultas y les medimos el tiempo.Estas consultas son sin agregar los indices.

    SELECT IdProducto, sum(Precio*Cantidad) FROM venta GROUP BY IdProducto;
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/bf2d0e48-e53f-49dd-bcf9-21b11121fa6a)

    SELECT Fecha,sum(Cantidad) FROM compra;
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/cc4a897b-26d0-4425-b1fe-4d70f06b05a2)

    SELECT cIdProveedor,p.Nombre FROM compra c JOIN proveedor p USING (IdProveedor);
![image](https://github.com/ylathan/Herramientas-de-BigData/assets/98925562/6e5f1788-3e3f-4df3-bbb1-f970fdea6c3d)


Insertamos los indices

        CREATE INDEX index_name
         ON TABLE base_table_name (col_name, ...)
         AS index_type
         [WITH DEFERRED REBUILD]
         [IDXPROPERTIES (property_name=property_value, ...)]
         [IN TABLE index_table_name]
         [ [ ROW FORMAT ...] STORED AS ...
         | STORED BY ... ]
         [LOCATION hdfs_path]
         [TBLPROPERTIES (...)]
         [COMMENT "index comment"];

         CREATE INDEX index_venta_producto ON TABLE venta(IdProducto) AS 'org.apache.hadoop.hive.ql.index.compact.CompactIndexHandler' WITH DEFERRED REBUILD;








   










        


