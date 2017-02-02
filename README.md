# MongoDB - Instalación y primeros pasos

## Instalación de MongoDB

### Importar la clave pública

```console
  La instalación ha sido probada en **Ubuntu 16.10**. Para otras plataformas ir a la [página oficial de MongoDB](https://www.mongodb.com/es).
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


