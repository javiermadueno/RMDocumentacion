##Utilización de las plantillas

Se va a proceder a la eliminación de la tabla `PlantillaModelo` y por tanto, la eliminación de las tablas `GrupoSlotsModelo` y `SlotModelo`.

A partir de ahora el comportamiento de las plantillas será el siguiente:

1. No se podrá eliminar o modificar la plantilla en los siguientes casos:

	- Cuando la plantilla sea una plantilla modelo (tendrá el campo `esModelo` a 1)
	- Cuando la comunicación se haya sido generada (tendrá el campo `generada` a 1)


###Plantillas Modelo

Cuando la plantilla sea una plantilla modelo, no se permitirá la importación, la exportación o la modificación del número de grupos de slots.

Si se podrá, sin embargo, la subida de nuevos archivos ficheros html, que cumpliendo la estructura de la plantilla modifiquen el estilo de la misma. 

###Plantillas
Si el cliente ha creado una plantilla desde 0, podrá realizar cualquier modificación siempre y cuando se cumplan las condiciones del apartado 1.	