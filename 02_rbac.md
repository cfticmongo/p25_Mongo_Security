# Control de accesos basado en roles

## Términos

- Roles. Agrupaciones lógicas del conjunto de privilegios que son asignados a los usuarios. En MongoDb
se pueden usar los roles del sistema disponibles pero para mayor control se pueden crear a medida.

- Privilegios. Especificación del permiso para realizar operaciones sobre los recursos.

- Recursos. Los componentes del sistema sobre los que podemos operar, bases de datos, colecciones, índices, etc.

- Acciones. Tipo de operaciones que podemos llevar a cabo en MongoDB sobre los recursos, lectura, escritura, borrado, backup, creación de índices, etc.

## Usuarios administradores

Procedimiento antes de lanzar a producción el/los servidores

1.- Levantamos el servidor o cluster sin autenticación

```
mongod --dbpath <directorio>
```

2.- Nos conectamos con la shell

```
mongo
```

3.- Creamos el usuario administrador

Se debe crear a nivel de la base de datos admin

```
use admin

db.createUser({
    user: "juan",
    pwd: "4321", // no lo haga en casa :)
    roles: [
        {role: "userAdminAnyDatabase", db: "admin"},
        {role: "readWriteAnyDatabase", db: "admin"}
    ]
})
```

4.- Reiniciamos el servidor con autenticación

```
mongod --dbpath <directorio> --auth
```

5.- Nos conectamos con la shell con el usuario administrador


```
mongo --authenticationDatabase "admin" -u "juan" -p 
```

## Tareas de administración de usuarios

Como ya tenemos un usuario con permisos de administración, entre otras cosas
ya podemos crear nuevos usuarios.

Crear un nuevo usuario con solo permisos de lectura a una base de datos.

Estando logueados como juan/4321:

```
use clinica

db.createUser({
    user: "clinicaPublicAPI",
    pwd: '1122',
    roles: ["read"]
})
```

A continuación abrimos otra shell y nos logueamos como clinicaPublicAPI/1122

```
mongo --authenticationDatabase "clinica" -u "clinicaPublicAPI" -p 
```

Solo tendremos acceso a clinica

```
show dbs

use clinica

show collections // tendremos todas las colecciones

db.empleados.find() ok

db.empleados.insert({nombre: "Lourdes"}) // Error porque el rol read solo permite lecturas
WriteCommandError({
        "ok" : 0,
        "errmsg" : "not authorized on clinica to execute command { insert: \"empleados\", ordered: true, lsid: { id: UUID(\"c7b7a9e4-8835-481a-9a9f-496d943cfcb8\") }, $db: \"clinica\" }",
        "code" : 13,
        "codeName" : "Unauthorized"

## Almacenamiento de datos de usuarios.

Con permisos de usuario podemos desde la colección system.users acceder a todos los usuarios

```
use admin

 db.system.users.find().pretty()
{
        "_id" : "maraton.maratonAPI",
        "userId" : UUID("00097854-97ae-407f-b5a1-74141e08380c"),
        "user" : "maratonAPI",
        "db" : "maraton",
        "credentials" : {
                "SCRAM-SHA-1" : {
                        "iterationCount" : 10000,
                        "salt" : "9IGkwwSQy4On45F11STngg==",
                        "storedKey" : "92t7KUY708cXvEGSFKo3Zn9FwzM=",
                        "serverKey" : "GO2e73x5EifvJzUYCVuBsQ4xHXY="
                },
                "SCRAM-SHA-256" : {
                        "iterationCount" : 15000,
                        "salt" : "hjmtXaDj/fsZw1/XudusmWqJhHrilThZxLftUA==",
                        "storedKey" : "KAaKMA7xVrx85i8qN6jUONZeUt2WANnIO0N3bSSEezk=",
                        "serverKey" : "lszsdt1ZaqEHh+nwJB+NLjUhopLIiH0hRmtCie3j2sU="
                }
        },
        "customData" : {
                "api" : "maratonAPP"
        },
        "roles" : [
                {
                        "role" : "readWrite",
                        "db" : "maraton"
                }
        ]
}
{
        "_id" : "admin.juan",
        "userId" : UUID("d61d6d65-0313-4dbc-a179-ec257f31509b"),
        "user" : "juan",
        "db" : "admin",
        "credentials" : {
                "SCRAM-SHA-1" : {
                        "iterationCount" : 10000,
                        "salt" : "p4RFvBVByOHaqWlBkI6LLQ==",
                        "storedKey" : "bZX6DYaXz1ROgVFnKpCik6eEZu8=",
                        "serverKey" : "5sxdXx0Ik3n43bYSZBm+h01AM7E="
                },
                "SCRAM-SHA-256" : {
                        "iterationCount" : 15000,
                        "salt" : "MrS1xLCcJmoYdEBKnMzYsXfxe4ZoQaMmrHnmdg==",
                        "storedKey" : "2jqIqvBkdoY9n1I39S6M6Jfq9yf73JcpF4K/82SFgGg=",
                        "serverKey" : "WE4T4sWugfTNZMN1z89FskxRrkesbLLk8VDywJu5stw="
                }
        },
        "roles" : [
                {
                        "role" : "userAdminAnyDatabase",
                        "db" : "admin"
                },
                {
                        "role" : "readWriteAnyDatabase",
                        "db" : "admin"
                }
        ]
}
{
        "_id" : "clinica.clinicaPublicAPI",
        "userId" : UUID("cd596f36-8ff2-421d-8099-428e2f5b7d0c"),
        "user" : "clinicaPublicAPI",
        "db" : "clinica",
        "credentials" : {
                "SCRAM-SHA-1" : {
                        "iterationCount" : 10000,
                        "salt" : "sQ/QGAzEqEQQkhllw04suQ==",
                        "storedKey" : "LCbr2AvYvalAYRGcWnu/6Ws7dao=",
                        "serverKey" : "6UTqfwXqfQLo2/q4FRQOXg5gguw="
                },
                "SCRAM-SHA-256" : {
                        "iterationCount" : 15000,
                        "salt" : "KKZXXbAX3CoeuJPFSUKvctQr1EA2fBa+3QK8JQ==",
                        "storedKey" : "eqy7UZneDF0JqpKQrq3EbdPRUdEEB5YU1nbI0xflBP0=",
                        "serverKey" : "Ah53QziHBdPhS0oh3ADngzYzp7QDJ4qy9rUXhL8e33U="
                }
        },
        "roles" : [
                {
                        "role" : "read",
                        "db" : "clinica"
                }
        ]
}

## Cambiar la contraseña de un usuario

Con permisos de administrador, vamos a la base de datos en la que está
autenticado el usuario y usamos el comando runCommand de la
siguiente forma.

```
use clinica

db.runCommand({
    updateUser: "clinicaPublicAPI",
    pwd: "NoHay2sin3"
})
```

## Cambiar los roles de un usuario o extenderlos a otras bases de datos

Con permisos de administrador

```
use maraton

db.runCommand({
    updateUser: "maratonAPI",
    roles: [
        {
            "role" : "readWrite",
            "db" : "maraton"
        },
        {
            "role" : "read",
            "db": "gimnasio"
        }
    ]
})
```



