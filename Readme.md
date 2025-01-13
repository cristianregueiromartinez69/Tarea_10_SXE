# TAREA 10 SXE :smile:
## INSTALACIÓN MEDIANTE DOCKER COMPOSE DE ODOO(VERSIÓN 17, COMMUNITY)

**Ïndice** 😎
- Creación del archivo docker-compose-yml y pasos previos
- Creación estructura docker-compose odoo
- Creación estructura docker-compose postgresSQL
- Creación estructura docker-compose PgAdmin
- Verificación instalación odoo
- Verificación instalación PgAdmin

### 1. Creación del archivo docker-compose-yml y pasos previos 😄

**Requisitos previos** 😱
1. Conexión a intener
2. Entorno de trabajo con docker instalado

Si no tienes docker instalado, te dejo un enlace [aquí](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-es)

```bash
#Una vez tienes todo, ejecuta el siguiente comando en tu terminal en el home o donde prefieras
mkdir dockerOdoo
cd dockerOdoo
nano docker-compose.yml
```

Se nos abrirá un archivo nano para hacer el docker-compose.yml, el nombre de la carpeta puede ser el que quieras

### 2. Creación estructura docker-compose odoo 😄
```bash
#Copia lo siguiente en el docker compose
services: #array de los servicios que vamos a utilizar
  web: #nombre del servicio de odoo
    image: odoo:17.0 #descargamos la versión 17, pese a que la más reciente es la 18, pero la 17 recibe mantenimiento
    container_name: odooWebContainer #nombre del contenedor
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    depends_on: #indicamos que odoo depende de la base de datos para iniciar para que odoo no inicie antes
      - db
    ports: #indicamos que el servicio será accesible desde el puerto 8069, ya que si no no podríamos entrar a odoo
      - "8069:8069"
    environment: #variables de entorno del servicio de odoo
      HOST: db
      USER: odoo
      PASSWORD: myodoo
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - odoo-web-data:/var/lib/odoo
```

### 3.  Creación estructura docker-compose postgresSQL 😄


```bash
#copia y pega esto en el docker-compose.yml justo después que lo que ya escribiste de odoo
 db: #nombre del servicio de la base de datos
    image: postgres:15 #imagen que vamos a descargar de postgresSQL
    container_name: postgresSQLContainer #nombre del contenedor
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    environment: #variables de entorno del servicio
      POSTGRES_DB: postgres
      POSTGRES_PASSWORD: myodoo
      POSTGRES_USER: odoo
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - odoo-db-data:/var/lib/postgresql/data
    ports: #hacemos que sea accesible desde el puerto 5432, que es el puerto por defecto para postgresSQL
      - "5432:5432"
```

### 4. Creación estructura docker-compose PgAdmin 😄

```bash
#copia y pega esto en el docker-compose.yml justo después que lo que ya escribiste de odoo y lo de postgresSQL
 pgadmin: #nombre del servicio para el gestor de la base de datos
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    image: dpage/pgadmin4:latest #nos descargamos la versión más reciente de PgAdmin
    container_name: PgAdminContainer #nombre del contenedor
    depends_on: #indicamos que PgAdmin depende de la base de datos para iniciar para que PgAdmin no inicie antes
      - db
    ports: #Hacemos que sea accesible desde el puerto 5050 para poder entrar al servicio desde fuera de docker
      - "5050:80"
    environment: #variables de entorno del gestor de base de datos
      PGADMIN_DEFAULT_EMAIL: cr.m23@hotmail.com
      PGADMIN_DEFAULT_PASSWORD: PgAdminPassword
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - pgadmin-data:/var/lib/pgadmin

volumes: #Los volumenes de arriba, los tenemos que declarar fuera para que sean accesibles
  odoo-web-data: #volumen de persistencia de datos de odoo
  odoo-db-data: #volumen de persistencia de datos de postgresSQL
  pgadmin-data: #volumen de persistencia de datos de PgAdmin
```

**Resultado final** :hushed:
Si seguiste los pasos anteriores, tu docker-compose.yml se debe de ver así

```bash
services: #array de los servicios que vamos a utilizar
  web: #nombre del servicio de odoo
    image: odoo:17.0 #descargamos la versión 17, pese a que la más reciente es la 18, pero la 17 recibe mantenimiento
    container_name: odooWebContainer #nombre del contenedor
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    depends_on: #indicamos que odoo depende de la base de datos para iniciar para que odoo no inicie antes
      - db
    ports: #indicamos que el servicio será accesible desde el puerto 8069, ya que si no no podríamos entrar a odoo
      - "8069:8069"
    environment: #variables de entorno del servicio de odoo
      HOST: db
      USER: odoo
      PASSWORD: myodoo
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - odoo-web-data:/var/lib/odoo

  db: #nombre del servicio de la base de datos
    image: postgres:15 #imagen que vamos a descargar de postgresSQL
    container_name: postgresSQLContainer #nombre del contenedor
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    environment: #variables de entorno del servicio
      POSTGRES_DB: postgres
      POSTGRES_PASSWORD: myodoo
      POSTGRES_USER: odoo
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - odoo-db-data:/var/lib/postgresql/data
    ports: #hacemos que sea accesible desde el puerto 5432, que es el puerto por defecto para postgresSQL
      - "5432:5432"

  pgadmin: #nombre del servicio para el gestor de la base de datos
    restart: unless-stopped #indicamos que el servicio no se va a detener a menos que lo hagamos nosotros
    image: dpage/pgadmin4:latest #nos descargamos la versión más reciente de PgAdmin
    container_name: PgAdminContainer #nombre del contenedor
    depends_on: #indicamos que PgAdmin depende de la base de datos para iniciar para que PgAdmin no inicie antes
      - db
    ports: #Hacemos que sea accesible desde el puerto 5050 para poder entrar al servicio desde fuera de docker
      - "5050:80"
    environment: #variables de entorno del gestor de base de datos
      PGADMIN_DEFAULT_EMAIL: cr.m23@hotmail.com
      PGADMIN_DEFAULT_PASSWORD: PgAdminPassword
    volumes: #realizamos la persistencia de datos ya que si se corrompe el servicio y no hacemos esto, perdemos todo
      - pgadmin-data:/var/lib/pgadmin


volumes: #Los volumenes de arriba, los tenemos que declarar fuera para que sean accesibles
  odoo-web-data: #volumen de persistencia de datos de odoo
  odoo-db-data: #volumen de persistencia de datos de postgresSQL
  pgadmin-data: #volumen de persistencia de datos de PgAdmin
```

