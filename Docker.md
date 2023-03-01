## Docker
Curso de docker
- Un contenedor es una forma de poder empaquetar nuestras aplicaciones con todas las dependencias que éste tenga, incluyendo sus archivos de configuración.

Al estar empaquetados los contenedores son Portables, esto quiere decir que son fácil de compartir entre desarrolladores y los equipos de operaciones.

Los contenedores se almacenan en un repositorio de contenedores, es parecido a lo que pasa con Github. Tenemos repositorios públicos y privados. El repositorio público más conocido es DockerHub, ahí podemos encontrar por ejemplo contenedores de NodeJs, Python, mysql, etc.

Primero hay que descargar una imagen basada en Linux.

El equipo de desarrollo y de operaciones de despliegue se encargan en conjunto de construir una imagen. Aunque algunas veces solo las construya el equipo de desarrollo.

La única dependencia que necesita esta imagen para poder ejecutarse es el runtime de Docker, esto se automatiza gracias a los pipelines que tienen algunos proveedores de servicio para control de versiones, como bitbucket, github y gitlab.

Una imagen es el empaquetado que es lo que contiene las dependencias, el código y es lo que se comparte. Un contenedor es finalmente la imagen que nosotros configuramos pero con la cual nosotros ejecutamos un comando la cual va a mantener todas las dependencias corriendo en conjunto con su entorno y variables de entorno.

Un container son capaz tras capaz de imágenes, la capa de más abajo va a ser por lo general una distribución de Linux, la más utilizada suele ser Alpine de Linux por ser ligera. Sobre la imagen de Linux podemos poner más y más imágenes hasta que llegamos hasta la capa de la aplicación, la cual podría ser por ejemplo mysql,nodejs,Python,php, etc. Estos contenedores pesan mucho menos de lo que pesarían las máquinas virtuales.

La virtualización consiste en el hardware, por encima el kernel y sobre él la capa de aplicaciones. Virtualizamos el kernel y las aplicaciones, esto es muy pesado.

En el caso de Docker se virtualiza solamente la capa de aplicaciones, y en el caso del kernel que es el encargado de poder comunicar el hardware con las aplicaciones, Docker se encarga de utilizar el kernel del sistema operativo en el cual se está ejecutando.

- Docker Desktop es una máquina virtual, se encuentra optimizada, corre Linux y permite ejecutar containers. Además permite acceder al sistema de archivos y a la red, tanto interna como externa. Docker Desktop no es una única herramienta, sino que viene incluida de varias. Una de esas herramientas es Docker Compose y la línea de comandos (CLI). Y Docker Desktop puede correr de manera nativa en Windows gracias a la herramienta WSL2(Windows Subsystem for Linux).


```bash
docker pull node
```
 Descarga la imagen de node. Al no especificar la versión descargará la última imagen que se encuentre disponible.
 
```bash
docker image rm [image id]
```
Así elimino una imagen.

```bash
docker pull mongo
```
Así descargo la imagen de mongo. También podría definir la versión por ejemplo poniendo docker pull mongo:16

```bash
docker create mongo
```
(mongo es el nombre de la imagen que tengo descargada) -> Creo el contenedor usando la imagen de mongo que tengo descargada.
Docker créate mongo es una forma más corta de escribir Docker container create mongo

```bash
Docker start [id container]
```
Para iniciar el contenedor.

Con Docker ps veo la lista de mis containers. Aquí me sale el ID del container, luego la imagen en base a cual creé mi contenedor, luego el comando que utiliza el contenedor para poder ejecutarse.

```bash
docker stop [id container]
```
Detengo el contenedor.

```bash
docker ps -a
```
Le decimos a Docker que nos muestre todos los contenedores que se encuentran en nuestro sistema.


```bash
docker rm [name container]
```
Borrar contenedor.

```bash
docker create --name monguitoPrueba mongo
```
Luego de  - -name ponemos el nombre que queremos ponerle al contenedor, y a continuación ponemos el nombre de la imagen en base a la cual se creará ese contenedor.

```bash
docker start monguitoPrueba
```
Inicio el contenedor.

Ahora si nosotros intentáramos acceder a este contenedor para poder utilizar mongo no vamos a poder porque no se encuentra ningún puerto publicado para que nosotros podamos utilizar este contenedor. A pesar que nos diga que el puerto 27017 es el que usa mongo si nosotros intentáramos acceder a este no vamos a poder hacerlo.
Debemos indicarle a docker que el puerto de nuestro computador que va a utilizar.

## Mapear puertos
El concepto de port mapping se basa que cuando nosotros recibimos una conexión a un puerto específico en nuestra máquina y nosotros queremos mapearlo a algún puerto de uno de nuestros contenedores.

Indicamos que todas las peticiones que lleguen a través de un determinado puerto de nuestro computador sean redirigidos al puerto de nuestro contenedor que querramos.

```bash
docker create -p27017:27017 --name monguitoPrueba mongo
```
Debemos indicar el puerto de la máquina física que nosotros tenemos que queremos mapear al contenedor.
-p27017

Después de eso debemos indicar el puerto del contenedor que vamos a crear que queremos mapear
:27017

Entonces el primero es el puerto de la máquina nuestra donde instalamos docker y el segundo es el puerto del contenedor que vamos a mapear con nuestra máquina.

```bash
docker logs --follow monguitoPrueba
```
Con esto puedo saber si nuestro servidor de mongo se ejecutó de manera correcta.
Le podemos indicar el id del container o el nombre que le asignamos

Con el --follow se queda esperando a que espere otro log, si no lo pusiéamos nos volvería a la terminal.

```bash
docker run
```
Este comando hará 3 cosas.
- Verá si se encuentra la imagen, si no se encuentra la descarga
- Creará un contenedor
- Inicia el contenedor

```bash
docker run -d mongo
```
El comando -d es para que me vuelva a la línea de comandos, es decir trabajar en modo dettached.

```bash
docker run --name monguitoPrueba -p27018:27018 -d mongo
```

Todos los contenedores se configuran distinto, algunos requieren más o menos parámetros de configuración.

Vamos a ver qué variables de entornos me pide mongo en dockerhub para configurarlo.

```bash
MONGO_INITDB_ROOT_USERNAME,
MONGO_INITDB_ROOT_PASSWORD
```

Creamos nuestro contenedor
```bash
docker create -p27017:27017 --name monguitoPrueba -e MONGO_INITDB_ROOT_USERNAME=nico -e MONGO_INITDB_ROOT_PASSWORD=password mongo
```
-e significa variable de entorno

```bash
docker start monguitoPrueba
```

## Dockerfile
Meter nuestra aplicación construida en nodeJs en nuestro contenedor:
Debemos crear un archivo Dockerfile.
En ese archivo escribimos las configuraciones de nuestro contenedor.
Aquí van las instrucciones que necesita nuestro contenedor para poder crearse.
Todas las imágenes que nosotros creemos deben basarse en alguna otra imagen.

Como queremos que nuestra imagen se base en node ponemos:
```Dockerfile
FROM node:18
```

Ahora creamos una carpeta donde meteremos el código fuente de nuestra aplicación.

Esta ruta es en referencia al mismo contenedor, esto no hace referencia a la máquina física, es en base al contenedor que estamos creando.

```Dockerfile
RUN mkdir -p /home/app
```

![[imagen-001.jpeg]]
From es el nombre de la imagen sobre la que quiero que se base

Run es la carpeta donde meteremos el código fuente de la aplicación.
Esta ruta es dentro del mismo contenedor. No hace referencia a mi sistema de archivos ni a mi máquina física. 

Esta carpeta /home/app es donde meteremos el código fuente de la aplicación. Lo de Index.js. node_modules etc.

Con copy le decimos dónde se encuentran los archivos de nuestro sistema anfitrión para que tome esos archivos y colocarlos en el contenedor. " . "

Luego definimos el destino donde copiaremos ese código fuente de nuestra aplicación. /home/app

A continuación exponemos un puerto para poder conectarnos a nuestro contenedor. Ponemos el mismo puerto que donde se exponía nuestra aplicación, como nuestra aplicación estaba exponiendo el puerto 3000 entonces usamos ese puerto.

Por último indicamos el comando que se debe ejecutar para que la aplicación corra.
Ponemos CMD y juego comando + argumentos.
Es decir node es el comando y el argumento es la ruta completa de nuestra aplicación.

Para que los contenedores puedan comunicarse entre sí usamos comandos tales como:
```bash
Docker network ls
Docker network create [name]
Docker network rm [name]
```
Los contenedores luego se comunicarán a través de sus nombres

![[imagen-002.jpeg]]
Entonces cambiamos localhost por el nombre de nuestro contenedor. Es decir monguito.

## Docker build

Luego usaremos docker build.
Build es el comando para crear imágenes en base a un archivo Dockerfile que hayamos creado antes.

El comando docker build recibe recibe 2 argumentos
El primero es el nombre que le asignaré a la imagen, acompañado de la etiqueta o tag que le asignaremos.
El segundo argumento es la ruta de dónde se encuentra mi proyecto o mi archivo Dockerfile.

```bash
docker build -t miapp:1 .
```
-t le damos nombre.
El punto es la ruta del directorio actual

```bash
docker create -p27017:27017 --name monguitoPrueba --network miredPrueba -e MONGO_INITDB_ROOT_USERNAME=nico -e MONGO_INITDB_ROOT_PASSWORD=password mongo
```
Ahora creamos nuestro nuevo contenedor de mongo pero asignándole la red.
Le pasamos los puertos, le ponemos nombre, le asignamos una red, le pasamos las variables de entorno para poder asignarle usuario y contraseña. Al final indicamos el nombre de la imagen en la cual nos estamos basando.

```bash
docker create -p3000:3000 --name chanchito --network miredPrueba miapp:1
```
Ahora vamos a crear el contenedor de la aplicación que nosotros acabamos de colocar dentro de una imagen.

```bash
docker start chanchito
```

Entonces, recapitulando. lo que hemos hecho hasta ahora ha sido:
- Descargar imagen
- Crear una red
- Crear contenedor:
	- Asignar puertos
	- Asignar un nombre al contenedor
	- Asignar variables de entorno
	- Especificar la red
	- Indicar la imagen con su respectiva etiqueta "Imagen:etiqueta"
Y hemos debido hacer esto para cada contenedor.

Afortunadamente podemos automatizar todo esto con:
## Docker compose
Agregamos un nuevo archivo a nuestro proyecto llamado
docker-compose.yml
Aquí adentro debemos indicar como primer atributo la versión que nosotors estamos utilizando para trabajar con él.

```bash
version: "3.9"
services:
	chanchito:
		build: .
		ports:
			- "3000:3000"
		links:
			- monguitoPrueba
	monguitoPrueba:
		image: mongo
		ports:
			- "27017:27017"
		environment:
			- MONGO_INITDB_ROOT_USERNAME=nico
			- MONGO_INITDB_ROOT_PASSWORD=password

```
Tenemos que especificiar la versión.
Luego especificamos los contenedores que vamos a utilizar.
El contenedor de chanchito es la aplicación en la cual me encuentro actualmente trabajando. Entonces a chanchito le debo indicar cómo se tiene que construir, los puertos que tiene que utilizar  y también si es que va a utilizar otro contenedor  que se encuentre especificado dentro de este archivo de docker-compose también se lo debemos indicar.
Con "build: ." le estamos diciendo que va a tener que construir la imagen que se encuentra en esta misma ruta.
Luego mapeamos puertos, primero ponemos el puerto de nuestra máquina anfitrión y luego el puerto del contenedor.
En links le vamos a indicar el nombre del contenedor que queremos mapear que utilice nuestro servicio (o contenedor) de chanchito.

En monguito le decimos en base a qué imagen vamos a crear este contenedor.
Luego indicamos los puertos.
El puerto de la izquierda es el del sistema anfitrión y el de la derecha el del contenedor.
Ahora asignaremos las variables de entorno.

A continuación veremos el comando docker compose
```bash
docker compose up
```

Con docker images veo que se ha creado una nueva imagen
Y con docker ps -a veo que se han creado 2 contenedores nuevos

Podemos eliminar nuestros contenedores con:
```bash
docker compose down
```

## Volumes
Con los volumentes puedo montar la carpeta en mi sistema operativo anfitrión para que no se pierdan los datos al borrarse el contenedor.

Tenemos 3 tipos de volúmenes:
- Anónimo: solo indicas la ruta. Docker se encarga de guardar esto donde él decida, lo malo es que no podemos referenciar estos volumenes para que los utilice otro contenedor.
- Anfitrión o host: decides qué carpeta montar y dónde montarla
- Nombrado: es como el anónimo pero la diferencia es que luego podré referenciar este volumen nombrado cuando monte un nuevo contenedor. De esta manera puedo reutilizarlo con la misma imagen y además también con otras imágenes que vaya creando en un futuro.

```bash
version: "3.9"
services:
	chanchito:
		build: .
		ports:
			- "3000:3000"
		links:
			- monguitoPrueba
	monguitoPrueba:
		image: mongo
		ports:
			- "27017:27017"
		environment:
			- MONGO_INITDB_ROOT_USERNAME=nico
			- MONGO_INITDB_ROOT_PASSWORD=password
		volumes:
			- mongo-data:/data/db
			# mysql -> /var/lib/mysql
			# postgres -> /var/lib/postgresql/data
volumes:
	mongo-data:
	otro-volumen:

```
La segunda vez que escribimos volumes indicamos el nombre que queremos asignarle a nuestro volumen. No es necesario poner datos luego de mongo-data: ya que docker compose sabrá qué hacer con esto. Aquí definimos todos los volumenes que van a utilizar nuestros contenedores.

La primera vez que escribimos volumes (luego de enviroments) es para indicarle al contenedor monguito cuáles son los volúmenes que este va a utilizar, pero solamente podemos mencionar volúmenes que hayamos definido en la sección volumes posterior.
Dentro de este primer volumes debemos agregar luego de mongo-data la ruta que se va a encontrar dentro del contenedor en donde va a ser montado el volumen de mongo-data.

Ahora si hacemos docker compose up, ejecutamos y luego hacemos docker compose down se mantienen los datos gracias a los volúmenes.

## Configurar ambientes de trabajo

Ahora hacemos un nuevo archivo llamado Dockerfile.dev
nodemon es para que se guarden los cambios al trabajar con nodejs
```bash
FROM node:18

RUN npm i-g nodemon
RUN mkdir -p /home/app

WORKDIR /home/app

EXPOSE 3000

CMD ["nodemon","index.js"]

```
Con el comando WORKDIR le decimos que la ruta donde vamos a estar trabajando es /home/app.
Cuando nosotros le indicamos esto le decimos a nuestro archivo de Dockerfile que vamos a trabajar en esa ruta en particular. Así que ahora la parte de CMD donde poníamos la ruta completa ya no es necesario, por eso ya no ponemos "CMD ["node","/home/app/index.js"]"

Como vamos a trabajar con un volume el cual se va a encargar de colocar un enlace simbólico en nuestra ruta de /home/app ya no necesitamos la línea de "COPY . /home/app"

Ahora que terminamos de crear este archivo de Dockerfile.dev tenemos que agregar otro archivo de docker compose llamado docker-compose-dev.yml
En este archivo docker-compose-dev.yml tendremos lo siguiente:

```bash
version: "3.9"
services:
	chanchito:
		build:
			context: .
			dockerfile: Dockerfile.dev
		ports:
			- "3000:3000"
		links:
			- monguitoPrueba
		volumes:
			- .:/home/app
	monguitoPrueba:
		image: mongo
		ports:
			- "27017:27017"
		environment:
			- MONGO_INITDB_ROOT_USERNAME=nico
			- MONGO_INITDB_ROOT_PASSWORD=password
		volumes:
			- mongo-data:/data/db

volumes:
	mongo-data:
	otro-volumen:

```
En la propiedad de build agregamos la propiedad context la cual le indica a este archivo de docker compose dónde se encuentra la aplicaicón o el contexto en el cual va a estar trabajando. Le ponemos punto porque esto es la ruta actual donde se encuentra el archivo de docker-compose-dev.
Ahora le indicamos que la imagen de chanchito la construya pero utilizando el archivo de Dockerfile.dev que acabamos de crear. Esto lo hacemos agregando una propiedad al mismo nivel de la propiedad de context, llamada dockerfile:

El último paso que nos queda es indicarle a nuestro contenedor de chanchito que utilice un volume. Aquí usaremos un volume anónimo. Indicamos que nuestra ruta actual es la que tiene que ser montada en el volumen y luego le indicamos la ruta del destino en el contenedor.

Ahora en la terminal ponemos:
```bash
docker compose -f docker-compose-dev.yml up
```

Con -f le indicamos que es un archivo docker compose completamente customizado que no sea docker-compose.yml

De esta manera es que podemos tener 2 ambientes de trabajo, uno de producción y otro de desarrollo.
