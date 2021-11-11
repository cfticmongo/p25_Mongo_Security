# Autenticación en MongoDB

## Mecanismos

- SCRAM Por defecto (certificación)

- x.509 Certificados de autenticación

Para la versión enterprise

- LDAP

- Kerberos authentication

## Establecimiento de autenticación

Por defecto un servidor mongod no tiene activada la autenticación, se activa con la
opcion --auth al levantar mongod.

## Procedimiento

Para poder usar autenticanción, previamente hay que generar al menos un usuario con
role que disponga de permisos según lo que vaya a hacer y posteriormente levantar
con autorización.

1.- En primer lugar levantamos mongod sin auth

```
mongod --dbpath <directorio>
```

2.- Nos conectamos con la shell para crear un usuario

```
mongo
```

Sintaxis

A nivel de base de datos

db.createUser({
    user: <nombre>,
    pwd: <contraseña>,
    customData: <datos-adicionales-opcionales>,
    roles: [
        {documento-de-especificación-rol} | <rol>,
        ...
    ]
})


Para comprobar el mecanismo de autenticación

Crear un usuario en una base de datos

```
use maraton

db.createUser({
    user: "maratonAPI",
    pwd: "1234",
    customData: {api: "maratonAPP"}, // totalmente opcional
    roles: [
        "readWrite"
    ]
})
```
Successfully added user: {
        "user" : "maratonAPI",
        "customData" : {
                "api" : "maratonAPP"
        },
        "roles" : [
                "readWrite"
        ]
}

Aunque el usuario se establezca a nivel de base de datos, los datos de este usuario y del resto
se gestionan de manera centralizada en la colección system.users de la base de datos admin.

3.- Reiniciamos el servidor para levantarlo con autenticación


```
mongod --dbpath <directorio> --auth
```

O en archivo de configuración .conf:

```
security:
    authorization: enabled
```

4.- Nos conectamos con la shell 

```
mongo --authenticationDatabase "maraton" -u "maratonAPI" -p 
```

Con esta conexión, que también podremos logicamente implementar en la URI de las aplicaciones, tendremos acceso solamente
a la base de datos maraton

```
show dbs // solo devuelve maraton
```

Y, de acuerdo al rol asignado, podrá realizar operaciones de escritura y lectura en las colecciones
de esa base de datos.