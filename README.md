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
