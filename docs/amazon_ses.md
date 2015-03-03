#Amazon SES

##Requisitos
* Verificar los dominios y cuentas de correo electrónico.


* Subscribirse al servicio. Una vez suscrito, se tendrá acceso al entorno de pruebas de **Amazon SES**, un entorno diseñado especialmente para probar y evaluar el servicio.


* Solicitar acceso a producción: Una vez que esté ya listo para utilizar Amazon SES y enviar correo electrónico, deberá solicitar acceso a producción. Solamente tardará unos minutos en solicitar acceso a producción; normalmente, recibirá una respuesta en un plazo de menos de 24 horas.


* Enviar correo electrónico: puede utilizar **SMTP** o la **API de Amazon SES** para poner en la cola de envío un correo.

##Interfaces de envío de correo electrónico

###SMTP
Se puede configurar para que todos los mensajes salientes se envien a traves del servidor de correo de Amazon SES.

Ejemplo de configuración en *Symfony2*:

	# app/config/config.yml
	swiftmailer:
	    transport:  smtp
	    host:       email-smtp.us-east-1.amazonaws.com
	    port:       465 # different ports are available, see SES console
	    encryption: tls # TLS encryption is required
	    username:   AWS_ACCESS_KEY  # to be created in the SES console
	    password:   AWS_SECRET_KEY  # to be created in the SES console


###API Amazon SES
Es la manera mas avanzada. Para acceder a la API se puede utilizar el SDK de Amazon Web Service (AWS) disponible en varios lenguajes (Java, PHP, C#)

[Instalación del SDK AWS de PHP](http://docs.aws.amazon.com/aws-sdk-php/guide/latest/installation.html)

[PHP SDK AWS Documentacion](http://docs.aws.amazon.com/aws-sdk-php/guide/latest/index.html#getting-started)

[Envio de Email con PHP](http://docs.aws.amazon.com/aws-sdk-php/guide/latest/service-ses.html)

##Límites
* Amazon SES acepta mensajes con un tamaño de hasta 10 MB. En esta capacidad se incluyen los archivos adjuntos que formen parte del mensaje.


* Amazon SES **permite especificar un máximo de 50 destinatarios para cada mensaje** que envíe. O lo que es lo mismo: el número total de destinatarios incluidos en los campos Para:, CC: y CCO: no debe ser superior a 50. Si necesita enviar un correo electrónico a más de 50 destinatarios, necesitará enviar varios mensajes, cada uno de ellos dirigido a 50 destinatarios o menos.


* Los nuevos usuarios de Amazon SES que hayan recibido acceso de producción pueden enviar hasta 10 000 mensajes de correo electrónico en un periodo de 24 horas, a un ritmo máximo de 5 correos electrónicos por segundo

##Precio
El precio es de **0.10$ por cada 1000 mensajes de correo**. Condiserando un mensaje de correo como una comunicación a cada destinatario.
En el caso de llevar archivos adjuntos se facturarán **0,12$ por GB** de archivos adjuntos enviados.
