# Roles built-in de MongoDb

https://docs.mongodb.com/manual/reference/built-in-roles/


## Roles de usuario de operaciones con bases de datos

read
readWrite

## Roles de usuario de administración de bases de datos

dbAdmin // Permite realizar todas las operaciones de administración de la base de datos
userAdmin // Administrar los usuarios de esa base de datos
dbOwner // Permite realizar todas las operaciones de la base de datos

## Roles de usuario de administración de todas las bases de datos
(Los usuarios que usen estos roles deben estar creados en la base de datos admin)

readAnyDatabase // Podría usarse para un cliente que necesite lecturas en todas las bases de datos
readAndWriteAnyDatabase // Permite a un DBA o DEV escribir y leer en todas las bases de datos
dbAdminAnyDatabase // Permite administrar todas las bases de datos
clusterMonitor // Permite acceder a métodos de monitorización de los cluster
clusterManager // Permite acceder a administrar los cluster
backup // Permite realizar operaciones de backup
restore // Permite realizar operaciones de restore de los backup

## Superusuarios

dbOwner // Si se asgina a la base de datos admin
root // Máximo permiso idem al anteior + clusterManager