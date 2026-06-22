# Jun22

Al intentar configurar la conexión de servicio en Azure DevOps utilizando el método automático recomendado (App registration (automatic)), el sistema arrojó un error de privilegios insuficientes en Microsoft Graph. Esto se debe a que las cuentas educativas, como tu suscripción de Azure for Students, tienen restringido por seguridad que los alumnos registren aplicaciones externas de forma automática en el directorio global administrado por la universidad. Además, inicialmente el desplegable del espacio de trabajo aparecía vacío porque requería la creación previa de un Azure Machine Learning Workspace dentro de tu grupo de recursos.

La solución: 
Para saltar este bloqueo de administración, creamos una Identidad Administrada (Managed Identity):
    mi-devops-conexion
directamente en tu panel de Azure, otorgándole el rol de Colaborador (Contributor) dentro del control de acceso (IAM) de tu nuevo espacio de trabajo de Machine Learning. Después, regresamos a Azure DevOps, cambiamos el tipo de identidad a Managed Identity para enlazar el recurso que habías creado y finalizamos la configuración guardando la conexión con el nombre:
    aml-workspace-connection-demo
, dejando marcada la casilla de seguridad para dar acceso libre a todos tus pipelines de despliegue.

resumen: 
En lugar de intentar fabricar una llave nueva en el servidor central, vamos a usar una credencial física que ya existe dentro de mi propio grupo de recursos". Al seleccionar esa opción en el desplegable, el sistema te permitió buscar y "enlazar" directamente la identidad que habías creado tú mismo en el portal de Azure (a la que previamente le habías dado el rol de Colaborador). De esta forma, Azure DevOps pudo usar esa identidad existente como un puente seguro para conectarse, esquivando por completo las restricciones del servidor central de la universidad.

