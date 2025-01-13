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
1. Conexión a intenet.
2. Entorno de trabajo con docker instalado.

Si no tienes docker instalado, te dejo un enlace [aquí](https://www.digitalocean.com/community/tutorials/how-to-install-and-use-docker-on-ubuntu-20-04-es)

```bash
#Una vez tienes todo, ejecuta el siguiente comando en tu terminal en el home o donde prefieras
mkdir dockerOdoo
cd dockerOdoo
nano docker-compose.yml
```

Se nos abrirá un archivo nano para hacer el docker-compose.yml, el nombre de la carpeta puede ser el que quieras.

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

### 5. Verificación instalación odoo 😄

Lo primero, como verificamos que se instala odoo 🤔, sigue estos pasos:
1. guarda el archivo docker-compose.yml.
2. en la misma carpeta donde está el docker-compose.yml, ejecuta esto en el terminal.

```bash
sudo docker compose up
#también podéis hacer
sudo docker compose up -d
#mejor usar el primero ya que este os dice los errores que puede haber en la instalación
```

Tendría que salir algo como esto.
![instalacionTodo2](https://github.com/user-attachments/assets/8f3be63e-65ec-492a-a940-cafbe643d8a7)

Ahora vamos a un navegador y escribimos en la url lo siguiente:
```bash
http://(ip de tu entorno de trabajo):8069
```
Si seguisteis los pasos al pie de la letra, os tendría que salir algo así.
![odoo1](https://github.com/user-attachments/assets/89ffc97f-77e2-4a5f-882a-e821eccbb2fa)

Nos pide que creemos una base de datos.

**Atención** 😱😱😱
No pongáis la base que creasteis en el docker-compose.yml ya que os dirá que ya existe y os dará error, poned otra distinta, como esta por ejemplo.
![odoo2](https://github.com/user-attachments/assets/9194960e-d0ee-434f-9630-f4410559dbd3)

Una vez hecho esto, le damos a crear database y esperamos un poco, tened paciencia 🥰. Después os redirigirá a esta página.
![odoo3](https://github.com/user-attachments/assets/fba0b7a8-383b-4378-a890-93b7c6919904)

Introducimos las credenciales que pusimos en la página anterior y le damos a enter. Nos debería de llevar a odoo como aquí.
![odoo4](https://github.com/user-attachments/assets/6b11e9ff-eef7-4d2e-9c04-c75ed0803aa0)

Como sabemos que hemos instalado la versión 17 y es la versión community 🤔, haced esto.
1. Nos vamos a settings dentro de odoo


![odoo5](https://github.com/user-attachments/assets/c956973b-f43e-4850-a07f-c641a73d660e)


2. Entramos y estaremos en esta página
![odoo6](https://github.com/user-attachments/assets/c57b2a48-dff5-4014-ade3-667bf5e523b5)
3. hacemos scroll hacia abajo y ahí estará la versión de odoo y la community al lado
![odoo7](https://github.com/user-attachments/assets/9fa361a5-601f-4dcb-97bc-a465002c753f)

####   FELICIDADES, HAS INSTALADO ODOO CON DOCKER COMPOSE, AHORA PODRÁS TENER BIEN ORDENADO TODO TU NEGOCIO/EMPRESA, BUEN TRABAJO 🥳🥳🥳

### 6. Verificación instalación PgAdmin 😄
Como ya tenemos el docker-compose.yml levantado, simplemente abrimos un navegador, nos vamos a la url y ponemos esto.

```bash
http://(ip de tu entorno de trabajo):5050
```
Nos debería de salir algo así.
![PgAdmin1](https://github.com/user-attachments/assets/b796afd0-0d09-4c21-8860-cbb754becb27)

Introducimos las credenciales, las que tenemos en el docker-compose.yml y le damos a enter. Nos debería de mandar aquí.
![PgAdmin2](https://github.com/user-attachments/assets/e708d3a8-de4b-40b6-8646-0cde3a3de29d)

**Problemas encontrados** :worried:
Al entrar en PgAdmin, no sale automáticamente el servicio de la base de datos vinculado, pero lo podemos vincular perfectamente desde aquí, sigue estos pasos.

- Le tenemos que dar donde pone Add new service, nos saldrá algo así.

![PgAdmin3](https://github.com/user-attachments/assets/27bd536c-e192-4ed7-91de-f8efbe2581e0)

Los apartados que nos interesan son General y connection. En general ponemos un nombre al servicio y listo, luego vamos a connect.

- Introducimos las credenciales como en la siguiente imagen y le damos a save.

![PgAdmin4](https://github.com/user-attachments/assets/921f7a37-70b4-4765-a221-40b7a8c307b9)

- Tenemos las bases creadas aquí.

![PgAdmin5](https://github.com/user-attachments/assets/f152b8be-612e-400c-9a29-11795d175492)

#### Como se puede observar en la imagen, tenemos tanto la base que se creó con el docker-compose.yml como la que creamos nosotros mismos en la pagina de odoo. Si te salió todo lo anterior, FELICIDADES 🥳🥳🥳, has instalado odoo, postgresSQL y PgAdmin con docker, mucha suerte en tus proyectos.





