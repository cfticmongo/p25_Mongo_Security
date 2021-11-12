# Roles propios en MongoDB

Podemos crear roles propios con unos determinados privilegios para combinación
de recurso/acciones y asignarlos a los usuarios que los necesiten.

Las acciones que se le asignan a los recursos están disponibles en:

https://docs.mongodb.com/manual/reference/privilege-actions/

Estos roles se determinan también a nivel de base de datos.

Sintaxis.

db.createRole({
    role: <nombre-del-rol>,
    privileges: [
        {resource: {db: <base-datos>, collection: <colección>}, actions: [<action1>, <action2>, ...]},
        ...
    ],
    roles: [
        <otro-rol-de-partida>
    ]
})

```
use maraton

db.createRole({
    role: 'leerTodasColeccionesEscribirRunners',
    privileges: [
        {
            resource: {db: 'maraton', collection: 'runners'},
            actions: ["find", "insert", "update"]
        }
    ],
    roles: [
        {role: "read", db: "maraton"}
    ]
})

```

Asignamos a un usuario

```
use maraton

db.createUser({
    user: 'maratonInscriptionAPI',
    pwd: '1234',
    roles: [
        "leerTodasColeccionesEscribirRunners"
    ]
})

```

Comprobamos con el nuevo usuario desde otra shell



```
mongo --authenticationDatabase "maraton" -u "maratonInscriptionAPI" -p 


use maraton

> db.runners.insert({nombre: 'test'}) OK
WriteResult({ "nInserted" : 1 })
> db.jueces.insert({nombre: 'test'}) // Error porque no tenemos permisos para esa colección
WriteCommandError({
        "ok" : 0,
        "errmsg" : "not authorized on maraton to execute command { insert: \"jueces\", ordered: true, lsid: { id: UUID(\"5b77ea87-cbd8-42c5-991e-45efa7a301f4\") }, $db: \"maraton\" }",
        "code" : 13,
        "codeName" : "Unauthorized"
})
> db.jueces.find({}) // Todos los find a cualquier colección serían ok


```

