# BILINGUISMO - DESPLIEGUE CON DOCKER EN EL TUMLAB

## Contenido
  * [Descripción](#descripcion)
  * [Prerrequisitos](#prerrequisitos)
  * [Despliegue en Docker utilizando el docker-compose](#despliegue)

<a name="descripcion"></a>
## Descripción

Este proyecto contiene la información necesaria para desplegar la solucón de Bilinguismo en el Tumlab utilizando Docker. 

### Software utilizado:

* Sistema operativo del Tumlab: Ubuntu server versión 20.04.05
* Docker: Versión 20.10.21
* Docker compose: Version 2.12.2
* MySQL: Versión 8.0.31
* PostgreSQL: Version 13

<a name="prerrequisitos"></a>
## Prerrequisitos

### Motores de bases de datos
En el equipo deben estar los motores de base de datos PostgreSQl y MySql correctamente instalados y aceptando conexiones externas. 

### Credenciales de acceso
Se deben solicitar las siguientes credenciales:

* Credenciales de acceso por SSH al Tumlab.
* Credenciales de acceso a MySQL.
* Credenciales de acceso a Postgres
* Usuario en el Ubuntu server con privilegios de root.

### Conexión al Tumlab mediante SSH
Para establecer la conexión puede utilizar el software PuTTY que puede descargar dando clic [aquí.][putty]

[putty]: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html

### Conecta el Tumlab a Internet
Conectar un cable de red con acceso a Internet en el purto LAN del Tumlab y verifique que tiene conexión a Internet.

<a name="despliegue"></a>
## Despliegue en Docker utilizando el docker-compose

### Clonar el proyecto:

Si la carpeta /tumlab/repositories no existe, ejecute los siguientes comandos:

```bash
    cd /tumlab
    mkdir repositories
    cd repositories
    git clone https://gitlab.com/bilinguismo/integration_tumlab.git
    cd integration_tumlab/
```

Si la carpeta /tumlab/repositories existe, ejecute los siguientes comandos:

```bash
    cd /tumlab/repositories
    git clone https://gitlab.com/bilinguismo/integration_tumlab.git
    cd integration_tumlab/
```

### Creación de usuario y bases de datos en MySql:

Para crear las bases de datos y un usuario para accederla, se deben ejecutar los siguientes comandos. En este punto es requerido el usuario de MySQL.  


* Acceso a mysql: Reemplazar la palabra (nombre_usuario) por el usuario con privilegios de root que usará para conectarse a la base de datos MySql 

```bash
    mysql -u nombre_usuario -p
```

* Creación del usuario:

```bash
    CREATE USER 'bilinguismo-db-user'@'%' IDENTIFIED BY 'Bilinguismo2022*';
```

* Creación y asignación de permisos sobre la base de datos bilinguismo_db:

```bash
    CREATE DATABASE bilinguismo_db;
    GRANT ALL PRIVILEGES ON bilinguismo_db.* TO 'bilinguismo-db-user'@'%';
    FLUSH PRIVILEGES;
```

* Creación y asignación de permisos sobre la base de datos moodle_db:

```bash
    CREATE DATABASE moodle_db;
    GRANT ALL PRIVILEGES ON moodle_db.* TO 'bilinguismo-db-user'@'%';
    FLUSH PRIVILEGES;
    Exit;
```

### Cargue de las bases de datos MySql

Para cargar la información en las bases de datos creadas en el paso anterior, se deben ejecutar los siguientes comandos:

* Base de datos bilinguismo_db:

```bash
    mysql -u root -p bilinguismo_db < databases/prod/backend_db.sql
```

* Base de datos moodle_db:

```bash
    mysql -u root -p moodle_db < databases/prod/moodle_db.sql
```

### Creación de usuario y base de datos en PostgreSQL:

Para crear la base de datos y un usuario para accederla, se deben ejecutar los siguientes comandos. En este punto es requerido el usuario de PostgreSQL.  


* Acceso a postgres:

```bash
    su postgres
```

* Creación del usuario:

```bash
    CREATE USER bilinguismo_db_user with encrypted password 'Bilinguismo2022*';
```

* Creación y asignación de permisos sobre la base de datos bilinguismo_db:

```bash
    CREATE DATABASE keycloak;
    GRANT ALL PRIVILEGES ON DATABASE keycloak to bilinguismo_db_user;
```

### Despliegue de la solución

Para el despliegue de la solución se deben ejecutar el siguiente comando:

```bash
    docker-compose -p "bilinguismo" up
```