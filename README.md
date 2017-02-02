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

## Lanzar el demonio

```console
sudo service mongod start
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


