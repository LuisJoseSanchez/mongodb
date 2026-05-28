# Introducción a MongoDB (actualizado a la versión 8)

## MongoDB con Docker

Usaremos un contenedor Docker para probar MongoDB sin tener que instalar ni configurar nada en la máquina local.

Tan solo necesitaremos el siguiente `docker-compose.yml`:

```yaml
services:
  mongodb:
    image: mongo:8
    ports:
      - 27017:27017
    volumes:
      - ./mongo:/data/db
    environment:
      - MONGO_INITDB_ROOT_USERNAME=admin
      - MONGO_INITDB_ROOT_PASSWORD=123
```

Se puede editar al gusto para cambiar el puerto o los datos del usuario administrador de la base de datos.

Nótese que la carpeta `/data/db` del contenedor está mapeada a la carpeta `mongo` de la máquina local. En esta carpeta se guardarán los datos, de tal forma que los podremos recuperar aunque se pare, o incluso se elimine, el contenedor.

## Pasos a seguir para ejecutar MongoDB

### 1. Clonar el repositorio

```console
git clone https://github.com/LuisJoseSanchez/mongodb.git
```

### 2. Entrar en la carpeta del repositorio

```console
cd mongodb
```

Si hacemos `ls`, debemos ver el archivo `docker-compose.yml`:

```console
ls
docker-compose.yml  README.md  .gitignore
```

### 3. Lanzar el contenedor

```console
docker compose up
```

Es conveniente abrir otra pestaña en la terminal para comprobar que el contenedor está funcionando:

```console
docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                                             NAMES
bec0ea6cdfa3   mongo:8   "docker-entrypoint.s…"   About a minute ago   Up About a minute   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp   mongodb-mongodb-1
```

### 4. Ejecutar un terminal de bash dentro del contenedor

Ejecutamos, de forma interactiva, el intérprete de comandos `bash` dentro del contenedor. En la práctica, esto significa meterse dentro del contenedor para ejecutar comandos.

```console
docker exec -it mongodb-mongodb-1 bash
```

Podemos echar un vistazo para ver los archivos que tenemos dentro del contenedor:

```console
root@bec0ea6cdfa3:/# ls
bin  boot  data  dev  docker-entrypoint-initdb.d  etc  home  js-yaml.js  lib  media  mnt  opt  proc  root  run	sbin  srv  sys	tmp  usr  var
```

### 5. Ejecutar el interfaz de línea de comandos de MongoDB

```console
mongosh -username admin
```

Se pedirá la clave que, tal como se especificó en el fichero `docker-compose.yml` es `123`.

A partir de ahora, ya podemos ejecutar comandos de MongoDB para crear bases de datos, crear colecciones, insertar documentos, mostrar listados, etc.

Nos podremos salir en cualquier momento del intérprete de MongoDB con `exit`.

De igual modo, si queremos salirnos del `bash` del contenedor, tecleamos otra vez `exit`.

Y si queremos parar el contenedor, nos situamos en la pestaña del terminal donde lo lanzamos y pulsamos las teclas `Control + C`.

## Ver bases de datos existentes

```console
> show databases
admin   100.00 KiB
config   12.00 KiB
local    72.00 KiB
```

## Usar una base de datos (existente o no)

```console
> use gestion
switched to db gestion
```

## Ver en qué base de datos nos encontramos

```console
> db
gestion
```

## Borrar una base de datos

```console
> use prueba
switched to db prueba
> db.dropDatabase()
{ ok: 1, dropped: 'prueba' }
```

## Crear objetos (documentos) en memoria

Antes de seguir, vamos a usar la base de datos `gestion`:

```console
> use gestion
switched to db gestion
```

Y ahora, crearemos dos usuarios y los guardaremos en memoria (de momento, no se guardan en disco).

```console
> var persona1 = {nombre: "Mario", apellido: "Neta"}
> var persona2 = {nombre: "Pere", apellido: "Gil", pais: "España"}
> persona1
{ "nombre" : "Mario", "apellido" : "Neta" }
> persona2
{ "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
```

## Grabar datos

Vamos a grabar datos en la colección `usuarios` dentro de la base de datos `gestion`. Una colección es el equivalente a una tabla en las bases de datos relacionales.

```console
> db.usuarios.insertOne(persona1)
{
  acknowledged: true,
  insertedId: ObjectId('6a15ac04bd74d90614d1a7bb')
}
> db.usuarios.insertOne(persona2)
{
  acknowledged: true,
  insertedId: ObjectId('6a15ac61bd74d90614d1a7bc')
}
> db.usuarios.insertOne({nombre: "Elba", apellido: "Lazo", edad: 24})
{
  acknowledged: true,
  insertedId: ObjectId('6a15ac6dbd74d90614d1a7bd')
}
```

Observa que los documentos insertados (los datos sobre personas) no tienen por qué ajustarse a la misma estructura, pueden ser heterogéneos y, por tanto, contener distinto número y tipo de campos.

## Ver las colecciones de la base de datos actual

```console
> show collections
usuarios
```

## Ver el contenido de una colección

Sería el equivalente a `SELECT * FROM usuarios` en una base de datos relacional.

```console
> db.usuarios.find()
[
  {
    _id: ObjectId('6a15ac04bd74d90614d1a7bb'),
    nombre: 'Mario',
    apellido: 'Neta'
  },
  {
    _id: ObjectId('6a15ac61bd74d90614d1a7bc'),
    nombre: 'Pere',
    apellido: 'Gil',
    pais: 'España'
  },
  {
    _id: ObjectId('6a15ac6dbd74d90614d1a7bd'),
    nombre: 'Elba',
    apellido: 'Lazo',
    edad: 24
  }
]
```

Observa que a cada elemento insertado se le asigna de forma automática un identificador único.

## Mostrar un solo documento

```console
> db.usuarios.findOne()
{
	"_id" : ObjectId("58937be7a70c3985de49a38f"),
	"nombre" : "Mario",
	"apellido" : "Neta"
}
```

## Búsqueda condicional I

```console
> db.usuarios.find({nombre: "Elba"})
[
  {
    _id: ObjectId('6a15ac6dbd74d90614d1a7bd'),
    nombre: 'Elba',
    apellido: 'Lazo',
    edad: 24
  }
]
>
> db.usuarios.find({pais: "España"})
[
  {
    _id: ObjectId('6a15ac61bd74d90614d1a7bc'),
    nombre: 'Pere',
    apellido: 'Gil',
    pais: 'España'
  }
]
```

## Búsqueda condicional II

`$ne` significa *not equal*, `$gt` es *greater than* y `$lt` es *less than*.

```console
> db.usuarios.find({pais: {$ne: "España"}})
[
  {
    _id: ObjectId('6a15ac04bd74d90614d1a7bb'),
    nombre: 'Mario',
    apellido: 'Neta'
  },
  {
    _id: ObjectId('6a15ac6dbd74d90614d1a7bd'),
    nombre: 'Elba',
    apellido: 'Lazo',
    edad: 24
  }
]
>
> db.usuarios.find({ edad: {$gt: 18}})
[
  {
    _id: ObjectId('6a15ac6dbd74d90614d1a7bd'),
    nombre: 'Elba',
    apellido: 'Lazo',
    edad: 24
  }
]
>
> db.usuarios.find({edad: {$lt: 18}})

```

## Insertar varios documentos al mismo tiempo

Mediante `insertMany` se pueden insertar varios documentos a la vez pasando un array como parámetro.

```console
> var p1 = {nombre: "Lola", apellido: "Mento", edad: 35}
> var p2 = {nombre: "Encarna", apellido: "Vales", edad: 17, pais: "USA"}
> db.usuarios.insertMany([p1, p2])
{
  acknowledged: true,
  insertedIds: {
    '0': ObjectId('6a15b091bd74d90614d1a7be'),
    '1': ObjectId('6a15b091bd74d90614d1a7bf')
  }
}
>
> db.usuarios.find({}, {_id: false})
[
  { nombre: 'Mario', apellido: 'Neta' },
  { nombre: 'Pere', apellido: 'Gil', pais: 'España' },
  { nombre: 'Elba', apellido: 'Lazo', edad: 24 },
  { nombre: 'Lola', apellido: 'Mento', edad: 35 },
  { nombre: 'Encarna', apellido: 'Vales', edad: 17, pais: 'USA' }
]
```


## Edición de un documento con `replaceOne()`

```console
> var persona = db.usuarios.findOne({ "_id" : ObjectId("6a15b091bd74d90614d1a7be")})

> persona
{
  _id: ObjectId('6a15b091bd74d90614d1a7be'),
  nombre: 'Lola',
  apellido: 'Mento',
  edad: 35
}

> persona.nombre = "Salva"
Salva

> persona
{
  _id: ObjectId('6a15b091bd74d90614d1a7be'),
  nombre: 'Salva',
  apellido: 'Mento',
  edad: 35
}

> db.usuarios.replaceOne({ _id: persona._id }, persona)
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

> db.usuarios.find({}, {_id: false})
[
  { nombre: 'Mario', apellido: 'Neta' },
  { nombre: 'Pere', apellido: 'Gil', pais: 'España' },
  { nombre: 'Elba', apellido: 'Lazo', edad: 24 },
  { nombre: 'Salva', apellido: 'Mento', edad: 35 },
  { nombre: 'Encarna', apellido: 'Vales', edad: 17, pais: 'USA' }
]
```

:warning: Para modificar y guardar un documento "único" en una variable, hay que usar `findOne()`, que devuelve un documento. Sin embargo, `find()` devuelve un cursor (lista de documentos) y haría falta convertir esa lista en array con `find().toArray()` y luego seleccionar el elemento.

## Edición de un documento sustituyendo el documento completo

Vamos a modificar la edad del usuario cuyo nombre es `Encarna`, cargando el documento en una variable y guardándolo con `replaceOne()`.

```console
> p = db.usuarios.findOne({nombre: "Encarna"})
{
  _id: ObjectId('6a15b091bd74d90614d1a7bf'),
  nombre: 'Encarna',
  apellido: 'Vales',
  edad: 17,
  pais: 'USA'
}

> p.edad = 18
18

> db.usuarios.replaceOne({nombre: "Encarna"}, p)
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

> db.usuarios.find({nombre: "Encarna"})
[
  {
    _id: ObjectId('6a15b091bd74d90614d1a7bf'),
    nombre: 'Encarna',
    apellido: 'Vales',
    edad: 18,
    pais: 'USA'
  }
]
```

## Edición parcial con `updateOne()` y `$set`

Para cambiar solo algunos campos (sin reemplazar todo el documento), conviene usar `updateOne()` con el operador `$set`. De nuevo vamos a modificar la edad de `Encarna`.

```console
> db.usuarios.updateOne({nombre: "Encarna"}, {$set: {edad: 19}})
{
  acknowledged: true,
  insertedId: null,
  matchedCount: 1,
  modifiedCount: 1,
  upsertedCount: 0
}

> db.usuarios.find({nombre: "Encarna"})
[
  {
    _id: ObjectId('6a15b091bd74d90614d1a7bf'),
    nombre: 'Encarna',
    apellido: 'Vales',
    edad: 19,
    pais: 'USA'
  }
]
```

## Realizar una copia de seguridad de una base de datos

Para grabar o restaurar copias de seguridad, se usan los comandos `mongodump` y `mongorestore` respectivamente.

Estos comandos se ejecutan desde el terminal de Linux (por ejemplo, dentro del contenedor con `bash`), no desde la *shell* de MongoDB.

Así que hay que salirse antes de usarlos:

```console
exit
```

Se puede especificar la ruta de destino donde se guardará la copia de seguridad con la opción `-o`. Si no se especifica la ruta de destino, se guarda dentro de una carpeta `dump` dentro del directorio actual.

Como el servidor exige autenticación, hay que indicar usuario y contraseña (las mismas del `docker-compose.yml`).

Antes de hacer la copia de seguridad, nos colocaremos en `/data/db/` que la tenemos mapeada a `mongo` en local. Así podremos ver en nuestro navegador de archivos cómo se van creando los ficheros.

```console
cd /data/db/
mongodump -u admin -p 123 --authenticationDatabase admin -d gestion
```

La copia de seguridad se guardan en formato BSON.

Los `.bson` de `mongodump` son binarios; un editor de texto no sirve para leerlos.

Se puede guardar en formato JSON con `mongoexport`:

```console
mongoexport -u admin -p 123 --authenticationDatabase admin -d gestion -c usuarios --jsonArray
```

## Restaurar una copia de seguridad

```console
mongorestore -u admin -p 123 --authenticationDatabase admin -d gestion dump/gestion
```


## Eliminar documentos

Vamos a eliminar todos los usuarios cuyo atributo `pais` sea `España`.

```console
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
> 
> db.usuarios.deleteMany({pais: "España"})
{
  acknowledged: true,
  deletedCount: 1
}
> 
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }

```


## Consultar un número concreto de campos

Vamos a mostrar un listado de los usuarios solo con los campos `nombre` y `edad`.

```console
> db.usuarios.find({}, {nombre: 1, edad: 1, _id:0})
{ "nombre" : "Mario" }
{ "nombre" : "Elba", "edad" : 24 }
{ "nombre" : "Salva", "edad" : 35 }
{ "nombre" : "Encarna", "edad" : 19 }
```


## Contar documentos

```console
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
> 
> db.usuarios.countDocuments()
4
```

## Consultas ordenadas

El valor `1` se utiliza para realizar una consulta en orden ascendente y el `-1` para descendente. Con `limit()` se puede limitar el resultado a un número máximo de documentos.

```console
> db.usuarios.find().sort( {apellido: 1} )
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
> 
> db.usuarios.find().sort( {apellido: -1} )
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
> 
> db.usuarios.find().sort( {apellido: -1} ).limit(3)
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
```


## La función `skip()`

La función `skip()` permite "saltar" un número determinado de documentos de la consulta.

```console
> db.usuarios.find().sort( {apellido: 1} ).limit(2)
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
> 
> db.usuarios.find().sort( {apellido: 1} ).skip(1).limit(2)
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
```

## Contar documentos en un cursor con `skip()` y `limit()`

`countDocuments()` cuenta todos los documentos de la colección (o los que cumplan un filtro si se indica). En cambio, si queremos saber cuántos documentos devuelve una consulta concreta tras aplicar `sort()`, `skip()` y `limit()`, convertimos el cursor en array y usamos `.length`:

```console
> db.usuarios.countDocuments()
4
> 
> db.usuarios.find().sort( {apellido: 1} ).skip(1).limit(2).toArray().length
2
```

## Agrupación de documentos

Antes de hacer pruebas con la agrupación de documentos, vamos a meter más datos en nuestra colección de usuarios.

```console
> db.usuarios.insertOne({nombre: "Elsa", apellido: "Pato", edad: 52, pais: "Portugal"})
{
  acknowledged: true,
  insertedId: ObjectId('5895b74415c260814ec7f139')
}
> db.usuarios.insertOne({nombre: "Armando", apellido: "Bronca", edad: 22, pais: "Francia"})
{
  acknowledged: true,
  insertedId: ObjectId('5895b77215c260814ec7f13a')
}
> db.usuarios.insertOne({nombre: "Leandro", apellido: "Gado", edad: 48, pais: "Venezuela"})
{
  acknowledged: true,
  insertedId: ObjectId('5895b7d915c260814ec7f13b')
}
> db.usuarios.insertOne({nombre: "Olga", apellido: "Seosa", edad: 29, pais: "España"})
{
  acknowledged: true,
  insertedId: ObjectId('5895b88815c260814ec7f13c')
}
> db.usuarios.insertOne({nombre: "Elena", apellido: "Nito", edad: 30, pais: "USA"})
{
  acknowledged: true,
  insertedId: ObjectId('5895b8ab15c260814ec7f13d')
}
> 
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 17, "pais" : "USA" }
{ "_id" : ObjectId("5895b74415c260814ec7f139"), "nombre" : "Elsa", "apellido" : "Pato", "edad" : 52, "pais" : "Portugal" }
{ "_id" : ObjectId("5895b77215c260814ec7f13a"), "nombre" : "Armando", "apellido" : "Bronca", "edad" : 22, "pais" : "Francia" }
{ "_id" : ObjectId("5895b7d915c260814ec7f13b"), "nombre" : "Leandro", "apellido" : "Gado", "edad" : 48, "pais" : "Venezuela" }
{ "_id" : ObjectId("5895b88815c260814ec7f13c"), "nombre" : "Olga", "apellido" : "Seosa", "edad" : 29, "pais" : "España" }
{ "_id" : ObjectId("5895b8ab15c260814ec7f13d"), "nombre" : "Elena", "apellido" : "Nito", "edad" : 30, "pais" : "USA" }
```

Vamos a mostrar todos los países de donde son los usuarios.

```console
> db.usuarios.aggregate([{$group: {_id: "$pais"}}])
{ "_id" : "Venezuela" }
{ "_id" : "Francia" }
{ "_id" : null }
{ "_id" : "España" }
{ "_id" : "USA" }
{ "_id" : "Portugal" }
```

Ahora igual pero diciendo cuántas veces se repite cada pais.

```console
> db.usuarios.aggregate([{$group: {_id: "$pais", repetidos: {$sum: 1}}}])
{ "_id" : "Venezuela", "repetidos" : 1 }
{ "_id" : "Francia", "repetidos" : 1 }
{ "_id" : null, "repetidos" : 3 }
{ "_id" : "España", "repetidos" : 2 }
{ "_id" : "USA", "repetidos" : 2 }
{ "_id" : "Portugal", "repetidos" : 1 }
```

Igual y además con la media de edad por pais.

```console
> db.usuarios.aggregate([
    {
      $group: {
        _id: "$pais",
        repetidos: {$sum: 1},
        "edad media": {$avg: "$edad"}
      }
    }
  ])
{ "_id" : "Venezuela", "repetidos" : 1, "edad media" : 48 }
{ "_id" : "Francia", "repetidos" : 1, "edad media" : 22 }
{ "_id" : null, "repetidos" : 3, "edad media" : 29.5 }
{ "_id" : "España", "repetidos" : 2, "edad media" : 29 }
{ "_id" : "USA", "repetidos" : 2, "edad media" : 23.5 }
{ "_id" : "Portugal", "repetidos" : 1, "edad media" : 52 }
```

Igual que todo lo anterior y además excluyendo los valores `null` para el atributo `pais`.

```console
> db.usuarios.aggregate([
    {
      $match: {pais: {$ne: null}}
    },
    {
      $group: {
        _id: "$pais",
        repetidos: {$sum: 1},
        "edad media": {$avg: "$edad"}
      }
    }
  ])
{ "_id" : "Venezuela", "repetidos" : 1, "edad media" : 48 }
{ "_id" : "España", "repetidos" : 2, "edad media" : 29 }
{ "_id" : "USA", "repetidos" : 2, "edad media" : 23.5 }
{ "_id" : "Portugal", "repetidos" : 1, "edad media" : 52 }
{ "_id" : "Francia", "repetidos" : 1, "edad media" : 22 }
```

## Consultas con expresiones regulares

Vamos a mostrar todos los usuarios cuyos apellidos contienen la letra "e".

```console
> db.usuarios.find( {apellido: /e/} )
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 17, "pais" : "USA" }
{ "_id" : ObjectId("5895b88815c260814ec7f13c"), "nombre" : "Olga", "apellido" : "Seosa", "edad" : 29, "pais" : "España" }
```

Usuarios cuyo nombre termina con la cadena "na".

```console
> db.usuarios.find( {nombre: /na$/} )
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 17, "pais" : "USA" }
{ "_id" : ObjectId("5895b8ab15c260814ec7f13d"), "nombre" : "Elena", "apellido" : "Nito", "edad" : 30, "pais" : "USA" }
```

Usuarios cuyo nombre comienza por "El".

```console
> db.usuarios.find( {nombre: /^El/} )
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("5895b74415c260814ec7f139"), "nombre" : "Elsa", "apellido" : "Pato", "edad" : 52, "pais" : "Portugal" }
{ "_id" : ObjectId("5895b8ab15c260814ec7f13d"), "nombre" : "Elena", "apellido" : "Nito", "edad" : 30, "pais" : "USA" }
> 
```

## Ver los documentos en un formato legible

En `mongosh`, los resultados de `find()` ya se muestran formateados (con sangría), sin necesidad del antiguo método `pretty()` del shell `mongo`.

```console
> db.usuarios.find().limit(3).pretty()
[
  {
    _id: ObjectId('58937be7a70c3985de49a38f'),
    nombre: 'Mario',
    apellido: 'Neta'
  },
  {
    _id: ObjectId('58937c23a70c3985de49a391'),
    nombre: 'Elba',
    apellido: 'Lazo',
    edad: 24
  },
  {
    _id: ObjectId('58938745a70c3985de49a392'),
    nombre: 'Salva',
    apellido: 'Mento',
    edad: 35
  }
]
```


## Crear colecciones con validación de atributos

Al crear una colección, se puede indicar a MongoDB que los atributos sean de un tipo concreto o que cumplan con un determinado patrón.

Vamos a crear una nueva colección llamada `empleado` cuyo atributo nombre será de tipo `string`, `sueldo` será de tipo `number` y `email` deberá contener un símbolo `@`.

```console
db.createCollection("empleado", {
  validator: {
    $and: [
      { nombre: {$type: "string"} },
      { sueldo: {$type: "number"} },
      { email: {$regex: /@/} }
    ]
  }
})
```

## Crear índices

```console
> db.usuarios.createIndex({nombre: 1})
nombre_1
```


