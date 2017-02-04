# MongoDB - Instalación y primeros pasos

## Instalación de MongoDB

Esta instalación ha sido probada en **Ubuntu 16.10**. Para otras plataformas ir a la [página oficial de MongoDB](https://www.mongodb.com/es).

### Importar la clave pública

```console
sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
```

### Crear una lista de ficheros para MongoDB

```console
echo "deb [ arch=amd64,arm64 ] http://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
```

### Actualizar la base de datos local que contiene información sobre los paquetes

```console
sudo apt-get update
```

### Instalar los paquetes de MongoDB

```console
sudo apt-get install -y mongodb-org
```

### Lanzar el demonio

```console
sudo service mongod start
```

### Arranque automático del servicio

Si vamos a utilizar con frecuencia MongoDB, lo mejor es hacer que el demonio se lance de forma automática cada vez que arranca el sistema.

```console
sudo systemctl enable mongod.service
```

## Ejecutar el interfaz de línea de comandos de MongoDB

```console
mongo
```

## Crear objetos (documentos)

```console
> var persona1 = {nombre: "Mario", apellido: "Neta"}
> var persona2 = {nombre: "Pere", apellido: "Gil", pais: "España"}
> persona1
{ "nombre" : "Mario", "apellido" : "Neta" }
> persona2
{ "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
```

## Ver bases de datos existentes

```console
> show databases
admin  0.000GB
local  0.000GB
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
{ "dropped" : "usuarios", "ok" : 1 }
```

## Grabar datos

Vamos a grabar datos en la colección `usuarios` dentro de la base de datos `gestion`. Una colección es el equivalente a una tabla en las bases de datos relacionales.

```console
> db.usuarios.insert(persona1)
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert(persona2)
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Elba", apellido: "Lazo", edad: 24})
WriteResult({ "nInserted" : 1 })
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
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
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
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
> 
> db.usuarios.find({pais: "España"})
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
```

## Búsqueda condicional II

`$ne` significa *not equal*, `$gt` es *greater than* y `$lt` es *less than*.

```console
> db.usuarios.find( { nombre: {$ne: "Elba"} } )
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
> 
> db.usuarios.find( { pais: {$ne: "España"} } )
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
> 
> db.usuarios.find( { edad: {$gt: 18} } )
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
> 
> db.usuarios.find( { edad: {$lt: 18} } )
```

## Insertar varios documentos al mismo tiempo

Mediante `insert` se puede insertar un elemento o bien un array con varios elementos.

```console
> var p1 = { nombre: "Lola", apellido: "Mento", edad: 35 }
> var p2 = { nombre: "Encarna", apellido: "Vales", edad: 17, pais: "USA" }
> db.usuarios.insert( [p1, p2] )
BulkWriteResult({
	"writeErrors" : [ ],
	"writeConcernErrors" : [ ],
	"nInserted" : 2,
	"nUpserted" : 0,
	"nMatched" : 0,
	"nModified" : 0,
	"nRemoved" : 0,
	"upserted" : [ ]
})
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Lola", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 17, "pais" : "USA" }
```


## Edición de un documento con `save()`

```console
> var persona = db.usuarios.findOne({ "_id" : ObjectId("58938745a70c3985de49a392")})
> persona
{
	"_id" : ObjectId("58938745a70c3985de49a392"),
	"nombre" : "Lola",
	"apellido" : "Mento",
	"edad" : 35
}
> persona.nombre = "Salva"
Salva
> persona
{
	"_id" : ObjectId("58938745a70c3985de49a392"),
	"nombre" : "Salva",
	"apellido" : "Mento",
	"edad" : 35
}
> db.usuarios.save(persona)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.usuarios.find()
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937beca70c3985de49a390"), "nombre" : "Pere", "apellido" : "Gil", "pais" : "España" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 17, "pais" : "USA" }
```

:warning: Cuidado al guardar el documento en una variable. Hay que usar `findOne()` ya que utilizando `find()` el valor de la variable se pierde. Otra opción es volcar el resultado de la consulta en un array con `find().toArray`.

## Edición de un documento con `update()`

Vamos a modificar la edad del usuario cuyo nombre es `Encarna`.

```console
> p = db.usuarios.findOne({nombre: "Encarna"})
{
	"_id" : ObjectId("58938745a70c3985de49a393"),
	"nombre" : "Encarna",
	"apellido" : "Vales",
	"edad" : 17,
	"pais" : "USA"
}
> p.edad = 18
18
> db.usuarios.update({nombre: "Encarna"}, p)
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.usuarios.find({nombre: "Encarna"})
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 18, "pais" : "USA" }
```

## Edición de un documento con `update()` y `$set`

De nuevo vamos a modificar la edad de `Encarna`.

```console
> db.usuarios.update( {nombre: "Encarna"}, {$set: {edad: 19} } )
WriteResult({ "nMatched" : 1, "nUpserted" : 0, "nModified" : 1 })
> db.usuarios.find({nombre: "Encarna"})
{ "_id" : ObjectId("58938745a70c3985de49a393"), "nombre" : "Encarna", "apellido" : "Vales", "edad" : 19, "pais" : "USA" }
```

## Realizar una copia de seguridad de una base de datos

Se puede especificar la ruta de destino donde se guardará la copia de seguridad con la opcion `-o`. Si no se especifica la ruta de destino, se crea el directorio `dump` dentro del directorio actual. Dentro de `dump` se crea otro directorio con el nombre de la base de datos, en este caso `gestion`. Dentro de este último directorio se guardan las colecciones de la base de datos en formato BSON. Observa que el comando `mongodump` se ejecuta desde el terminal de Linux, no desde la *shell* de MongoDB.

```console
$ mongodump -d gestion
```

## Restaurar una copia de seguridad

```console
$ mongorestore -d gestion dump/gestion
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
> db.usuarios.remove({pais: "España"})
WriteResult({ "nRemoved" : 1 })
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
> db.usuarios.find().count()
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

## La función `size()`

A diferencia de `count()`, el método `size()` ofrece la cuenta de la consulta una vez filtrada con `skip()`, `limit()`, etc.

```console
> db.usuarios.find().sort( {apellido: 1} ).skip(1).limit(2).count()
4
> 
> db.usuarios.find().sort( {apellido: 1} ).skip(1).limit(2).size()
2
```

## Agrupación de documentos

Antes de hacer pruebas con la agrupación de documentos, vamos a meter más datos en nuestra colección de usuarios.

```console
> db.usuarios.insert({nombre: "Elsa", apellido: "Pato", edad: 52, pais: "Portugal"})
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Armando", apellido: "Bronca", edad: 22, pais: "Francia"})
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Leandro", apellido: "Gado", edad: 48, pais: "Venezuela"})
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Olga", apellido: "Seosa", edad: 29, pais: "España"})
WriteResult({ "nInserted" : 1 })
> db.usuarios.insert({nombre: "Elena", apellido: "Nito", edad: 30, pais: "USA"})
WriteResult({ "nInserted" : 1 })
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
> db.usuarios.aggregate( [ {$group: {_id: "$pais"}} ] )
{ "_id" : "Venezuela" }
{ "_id" : "Francia" }
{ "_id" : null }
{ "_id" : "España" }
{ "_id" : "USA" }
{ "_id" : "Portugal" }
```

Ahora igual pero diciendo cuántas veces se repite cada pais.

```console
> db.usuarios.aggregate( [ {$group: {_id: "$pais", repetidos: {$sum: 1}}} ] )
{ "_id" : "Venezuela", "repetidos" : 1 }
{ "_id" : "Francia", "repetidos" : 1 }
{ "_id" : null, "repetidos" : 3 }
{ "_id" : "España", "repetidos" : 2 }
{ "_id" : "USA", "repetidos" : 2 }
{ "_id" : "Portugal", "repetidos" : 1 }
```

Igual y además con la media de edad por pais.

```console
> db.usuarios.aggregate( [ {$group: {_id: "$pais", repetidos: {$sum: 1}, "edad media": {$avg: "$edad"}}} ] )
{ "_id" : "Venezuela", "repetidos" : 1, "edad media" : 48 }
{ "_id" : "Francia", "repetidos" : 1, "edad media" : 22 }
{ "_id" : null, "repetidos" : 3, "edad media" : 29.5 }
{ "_id" : "España", "repetidos" : 2, "edad media" : 29 }
{ "_id" : "USA", "repetidos" : 2, "edad media" : 23.5 }
{ "_id" : "Portugal", "repetidos" : 1, "edad media" : 52 }
```

Igual que todo lo anterior y además excluyendo los valores `null` para el atributo `pais`.

```console
> db.usuarios.aggregate( [ {$match: {pais: {$ne: null}}}, {$group: {_id: "$pais", repetidos: {$sum: 1}, "edad media": {$avg: "$edad"}}} ])
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

## Ver los documentos en un formato "bonito"

El método `pretty()` nos permite ver los documentos de una manera bonita y legible. A continuación se compara una consulta sin `pretty()` y otra con `pretty()`.

```console
> db.usuarios.find().limit(3)
{ "_id" : ObjectId("58937be7a70c3985de49a38f"), "nombre" : "Mario", "apellido" : "Neta" }
{ "_id" : ObjectId("58937c23a70c3985de49a391"), "nombre" : "Elba", "apellido" : "Lazo", "edad" : 24 }
{ "_id" : ObjectId("58938745a70c3985de49a392"), "nombre" : "Salva", "apellido" : "Mento", "edad" : 35 }
> 
> db.usuarios.find().limit(3).pretty()
{
	"_id" : ObjectId("58937be7a70c3985de49a38f"),
	"nombre" : "Mario",
	"apellido" : "Neta"
}
{
	"_id" : ObjectId("58937c23a70c3985de49a391"),
	"nombre" : "Elba",
	"apellido" : "Lazo",
	"edad" : 24
}
{
	"_id" : ObjectId("58938745a70c3985de49a392"),
	"nombre" : "Salva",
	"apellido" : "Mento",
	"edad" : 35
}
```


## 

```console
```


## 

```console
```

## 

```console
```

## 

```console
```
