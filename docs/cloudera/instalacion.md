#Instalación de Cloudera

##Cambiar el archivo `host`
Primero se cambia el nombre del host y se le pone un FQDN

	sudo hostname master.rm.com

Después se actualiza el archivo `/etc/hosts/`:

	127.0.0.1		localhost
	192.168.100.81	master.rm.com	master
	

##Permitir el login con `root` por ssh

Modificar el archivo `/etc/ssh/sshd_config` y cambiar la directiva `PermitRootLogin` a:

	PermitRootLogin yes

Y reiniciar el servicio
	
	sudo service ssh restart


##Cambiar Swappiness

	$ sudo bash -c "echo 'vm.swappiness = 0' >> /etc/sysctl.conf"

	$ sudo sysctl vm.swappiness=0
	

## instalar el conector MySql de Java

	sudo apt-get install libmysql-java
    
    export CLASSPATH=$CLASSPATH:/usr/share/java/mysql-connector-java.jar
    
Esto es necesario para poder utilizar mysql en lugar postgressql en la instalación de ozzie y hive

##Instalar el repositorio de cloudera
Primero es necesario instalar el repositorio de cloudera para que solo descargue los paquetes originales

	wget http://archive.cloudera.com/cdh5/one-click-install/trusty/amd64/cdh5-repository_1.0_all.deb

Y lo instalamos utilizando el comando `dpkg`:

	sudo dpkg -i cdh5-repository_1.0_all.deb

Por último se actualizan las dependencias:

	sudo apt-get update

##Instalar Cloudera Manager Service
Descargamos el instalador

	wget http://archive.cloudera.com/cm5/installer/latest/cloudera-manager-installer.bin
Se le dan permisos de ejecución


	chmod u+x cloudera-manager-installer.bin

y se ejecuta con privilegios de `root`

	sudo ./cloudera-manager-installer.bin

Esto lanzara el instalador. Hay que aceptar todos los acuerdos de licencias y cuando termine de instalar se continua el proceso de instalación mediante un navegador web.
Para ello, accedemos a la siguiente dirección:

	http://ip_servidor:7180

*NOTA:* Tarda cierto tiempo en arrancar el servicio. Por lo que no es de extrañar que al principio la web no esté disponible.

Los datos de acceso por defecto son los siguientes:

 * **usuario:** admin
 * **password:** admin

Ahora hay que seguir los pasos del instalador:

 1. Elegimos la version **Cloudera Express**
 2. Especificamos los cluster en los que queremos instalar cloudera cluster01, cluster02...) dependiendo del nombre que hayamos indicado anteriormente en los archivos `/etc/hosts`y `/etc/hostnames`.
 3. Marcamos que instale el *kit de desarrollo SE Oracle de Java*
 4. **NO** activaremos el modo de usuario único.
 5. Para la gestion de cluster marcaremos que utilice el usuario `root` y la misma contraseña para todos los clusters.
 6. Instalamos la opcion **Hadoop centrales**
 7. En un paso se nos preguntará si queremos utilizar base de datos embebidas o especificar una conexión. Utilizamos mysql (hay que instalar el conector de java, y crear las bases de datos indicadas a mano. Antes de continuar comprobamos que la conexión es correcta)
 *nota:* Indicando en los parametros de conexion el nombre completo de la maquina no ha funcionado. Me ha funcionado cuando he puesto `localhost:3306`
 8. seguir el wizard dejando todos los valores por defecto.


##Configuración de permisos
Para evitar problemas con los permisos del sistema hay que añadir el usuario de nuestra máquina al grupo de administracion de Hadoop (en nuestro caso `icca`)

	$ groupadd supergroup
	$ usermod -a -G supergroup icca 

Con estos dos comandos primero generamos el grupo `supergroup` y con el segundo añadimos nuestro usuario a dicho grupo.


##Solucion de problemas

###Bloques subreplicados
Al tratarese de un singlenode hay que cambiar el factor de replicacion a 1 y modificar el factor de replicacion de los archivos ya creados con el siguiente comando:

	hadoop fs -setrep -R 1 /






	



