#Agregar Clientes y Usuarios al LDAP

##Creación de un cliente
Para crear un nuevo usuario en el LDAP basta con ejecutar el siguiente comando desde la carpeta de principal de `Symfony`:

```bash
$ php app/console ldap:cliente:create id_cliente
```

El parámetro `id_cliente` es obligatorio y representa un **identificador único** de la empresa. Este identificador se utilizará en el siguiente paso para definir la base de datos del cliente y  los parámetros de conexión en el aplicación Symfony.

Para completar el proceso es necesario añadir los parámetros de conexión al archivo de configuración `app/config/conf.yml`.

En la configuración de Doctrine debemos añadir una nueva conexión cuyo nombre sea idéntico al `id_cliente` utilizado anteriormente y un nuevo `EntityManager` que utilice esta conexión 

Configuración de la nueva conexión:

```yaml
#app/config/config.yml
doctrine:
    dbal:
        default_connection: default
        connections:
            default:
                driver:   "%database_driver%"
                host:     "%database_host2%"
                port:     "%database_port2%"
                dbname:   "%database_name2%"
                user:     "%database_user%"
                password: "%database_password%"
                charset:  UTF8

            id_cliente:
                driver:   
                host:     
                port:     
                dbname:  
                user:    
                password: 
                charset:  UTF8                
```

Configuración del EntityManager:

```yaml
#app/config/config.yml
doctrine:
    dbal:
        default_connection: default
        connections:
            default:
                driver:   "%database_driver%"
                host:     "%database_host2%"
                port:     "%database_port2%"
                dbname:   "%database_name2%"
                user:     "%database_user%"
                password: "%database_password%"
                charset:  UTF8
				...
	orm:
        default_entity_manager: default
        entity_managers:
            default:
                connection: default
                mappings:
                    RMCategoriaBundle: ~
                    RMClienteBundle: ~
                    RMComunicacionBundle: ~
                    RMPlantillaBundle: ~
                    RMProductoBundle: ~
                    RMSegmentoBundle: ~
                    RMStaticBundle: ~
                    RMDiscretasBundle: ~
                    RMIntDiscretasBundle: ~
                    RMLinealesBundle: ~
                    RMTransformadasBundle: ~
                dql:
                    string_functions:
                        GROUP_CONCAT: RM\ComunicacionBundle\Doctrine\Mysql\GroupConcat
            
            id_cliente:
                connection: id_cliente
                mappings:
                    RMCategoriaBundle: ~
                    RMClienteBundle: ~
                    RMComunicacionBundle: ~
                    RMPlantillaBundle: ~
                    RMProductoBundle: ~
                    RMSegmentoBundle: ~
                    RMStaticBundle: ~
                    RMDiscretasBundle: ~
                    RMIntDiscretasBundle: ~
                    RMLinealesBundle: ~
                    RMTransformadasBundle: ~
                dql:
                    string_functions:
                        GROUP_CONCAT: RM\ComunicacionBundle\Doctrine\Mysql\GroupConcat          
```

Es muy importante añadir todos los Bundles existentes en la aplicación a los `mappings` del `EntityManager`.


##Creación de un usuario.
Para la creación de un nuevo usuario se utiliza el comando:
 ```bash
$ php app/console ldap:user:create id_cliente
 ```

El `id_cliente` es obligatorio y se refiere al identificador único de empresa explicado en la sección anterior.

Una vez ejecutado este comando se le preguntará por los siguientes datos:

 * `nombre_usuario`: El nombre de usuario que se utilizará para el login.
 * `nombre`: Nombre real del usuario
 * `apellidos`: Apellidos del usuario
 * `email`: Email del usuario
 * `telefono`: Telefono del usuario
 * `contraseña`: La contraseña que se utilizará para el login.

Una vez concluido se le pedirá confirmación para la creación del usuario. Se debe pulsar la tecla `y` y pulsar `ENTER`.
 









