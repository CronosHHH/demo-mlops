# DOCKER -> La lunch box

## Indice

- [Idea general](#idea-general)
- [Conceptos clave](#conceptos-clave)
- [Imagen vs contenedor](#imagen-vs-contenedor)
- [Otros conceptos](#otros-conceptos)
- [Lenguaje basico de Docker](#lenguaje-basico-de-docker)
- [Primeros pasos](#primeros-pasos)
- [Dockerizar una app de React](#dockerizar-una-app-de-react)
- [Comandos utiles](#comandos-utiles)
- [Docker Compose y Docker Init](#docker-compose-y-docker-init)
- [Notas finales](#notas-finales)

## Idea general

Docker sirve para empaquetar una aplicacion con todo lo que necesita para ejecutarse y hacer que se comporte de forma consistente en distintos entornos.

### Ventajas principales

- **Consistencia entre entornos**: la aplicacion corre igual en desarrollo, pruebas y produccion.
- **Aislamiento**: cada contenedor se ejecuta separado de los demas.
- **Portabilidad**: una imagen se puede mover entre maquinas sin cambiar el codigo.
- **Eficiencia**: los contenedores comparten el kernel del host, asi que suelen ser mas ligeros que una maquina virtual.
- **Versionado**: las imagenes permiten conservar distintas versiones de la aplicacion.
- **Escalabilidad**: facilita levantar, duplicar y eliminar instancias rapidamente.
- **Integracion DevOps**: ayuda a conectar desarrollo, test y despliegue.

> Un contenedor es un proceso o conjunto de procesos aislado que corre sobre el kernel del host, no sobre un kernel invitado.

## Conceptos clave

### Imagen vs contenedor

#### Image

La **imagen** es el paquete ejecutable.

- Es ligera, autocontenida y reproducible.
- Incluye el codigo, runtime, dependencias, librerias y herramientas necesarias.
- Se parece a una receta: define como construir el plato.

#### Container

El **contenedor** es la instancia en ejecucion de una imagen.

- Es el entorno donde la imagen corre realmente.
- Se parece al plato ya cocinado siguiendo la receta.
- Mantiene el aislamiento, pero usa recursos del host.

## Otros conceptos

### Volumes

Los **volumes** son el mecanismo de persistencia de datos en Docker.

- Permiten compartir datos entre contenedores y el host.
- Hacen que la informacion sobreviva aunque el contenedor se borre.
- Piensalo como datos que viven fuera del contenedor.

### Docker Network

Las **networks** son los canales de comunicacion de Docker.

- Permiten que un contenedor hable con otro contenedor.
- Permiten que un contenedor se comunique con el mundo exterior.
- Mantienen el aislamiento mientras habilitan la conexion necesaria.

### Docker Workflow

Docker suele organizarse en tres piezas:

| Parte | Funcion |
| --- | --- |
| Docker Client | Interfaz con la que el usuario envia comandos, ya sea por CLI o por Docker Desktop. |
| Docker Host / Docker Daemon | Proceso de fondo que construye imagenes y gestiona contenedores. |
| Docker Registry / Docker Hub | Repositorio central donde se publican y descargan imagenes. |

## Lenguaje basico de Docker

| Comando | Definicion |
| --- | --- |
| `FROM ubuntu:20.04` | Define la imagen base sobre la que se construye la tuya. |
| `WORKDIR /path/to/workdir` | Establece el directorio de trabajo dentro del contenedor. |
| `COPY . /app` | Copia archivos del proyecto al contenedor. |
| `RUN npm run dev` | Ejecuta un comando durante el build de la imagen. |
| `EXPOSE 3000` | Documenta el puerto que la aplicacion usa dentro del contenedor. No publica el puerto por si solo. |
| `ENV KEY=VALUE` | Define variables de entorno dentro de la imagen o el contenedor. |
| `ARG NODE_VERSION=20` | Define una variable que solo existe durante el build. |
| `VOLUME /myvol` | Declara un punto de montaje para datos persistentes. |
| `CMD ["npm", "run", "dev"]` | Define el comando por defecto cuando arranca el contenedor. |

### ENTRYPOINT vs CMD

- `CMD` define el comportamiento por defecto y se puede sobrescribir facilmente.
- `ENTRYPOINT` fija el ejecutable principal del contenedor.

## Primeros pasos

### Levantar una imagen desde Docker Hub

```bash
docker pull ubuntu:stonking-20260612
docker run -it ubuntu:stonking-20260612
```

### Crear una imagen propia

```Dockerfile
FROM node:20-alpine

WORKDIR /app

COPY . .

CMD node hello.js
```

Construccion y ejecucion:

```bash
docker build -t hello-docker .
docker run hello-docker:latest
docker run -it hello-docker
```

## Dockerizar una app de React

### Crear el proyecto

```bash
npm create vite@latest react-docker
```

Elegir:

- React
- TypeScript
- No hacer `npm install` al principio

### Dockerfile de React

En `hello-docker/react-docker/Dockerfile` los comandos clave son:

| Comando | Para que sirve |
| --- | --- |
| `FROM node:20-alpine` | Usa una base ligera con Node.js. |
| `RUN addgroup app && adduser -S -G app app` | Crea un usuario no root para ejecutar la app con menos privilegios. |
| `USER app` | Cambia al usuario `app` antes de ejecutar comandos sensibles. |
| `WORKDIR /app` | Situa el trabajo dentro de `/app`. |
| `COPY package*.json ./` | Copia primero las dependencias para aprovechar la caché de Docker. |
| `RUN chown -R app:app .` | Asegura que el usuario `app` pueda escribir en el directorio. |
| `RUN npm install` | Instala dependencias dentro de la imagen. |
| `COPY . .` | Copia el resto del codigo del proyecto al contenedor. |
| `EXPOSE 5173` | Declara el puerto que usara Vite en runtime. |
| `CMD npm run dev` | Arranca la aplicacion cuando el contenedor se inicia. |

### Construir y ejecutar

```bash
docker build -t react-docker .
docker run react-docker
```

Al arrancar Vite suele aparecer algo como:

```text
➜  Local:   http://localhost:5173/
➜  Network: use --host to expose
```

### Por que no funciona al principio

`EXPOSE 5173` solo informa a Docker de que el contenedor usa ese puerto, pero no publica el puerto en tu maquina.

Para acceder desde el host necesitas **port mapping**:

```bash
docker run -p 5173:5173 react-docker
```

Ademas, Vite debe escuchar en todas las interfaces:

```json
{
  "scripts": {
    "dev": "vite --host"
  }
}
```

Despues de reconstruir la imagen, ya deberia verse:

```text
➜  Local:   http://localhost:5173/
➜  Network: http://172.17.0.2:5173/
```

## Comandos utiles

| Comando | Que hace |
| --- | --- |
| `docker ps` | Muestra los contenedores en ejecucion. |
| `docker stop <id>` | Detiene un contenedor. |
| `docker container prune` | Borra contenedores detenidos. |
| `docker rm <id> --force` | Elimina un contenedor incluso si sigue en ejecucion. |
| `docker tag react-docker renatorrvv/react-docker` | Asigna una etiqueta de Docker Hub a la imagen. |
| `docker push renatorrvv/react-docker` | Sube la imagen al registry de Docker Hub. |

## Docker Compose y Docker Init

Docker Compose automatiza la construccion y el arranque de contenedores usando un archivo `compose.yaml`.

### Flujo general

```bash
npm create vite@latest vite-project
docker init
sudo docker compose up
```

### Ajustes habituales en `compose.yaml`

- Cambiar el nombre del servicio `server` por `web`.
- Quitar variables de entorno que no se vayan a usar.
- Añadir volumes para desarrollo local:

```yaml
volumes:
  - .:/app
  - /app/node_modules
```

### Por que ayuda Compose

- Simplifica el arranque.
- Evita repetir varios comandos largos.
- Permite parar todo con `Ctrl + C` cuando se lanza en primer plano.

## Notas finales

- El login en Docker Desktop en Linux puede requerir la configuracion de credenciales indicada en la documentacion oficial.
- Para publicar imagenes, primero hay que autenticar con `docker login`.
- Docker AI se puede usar para consultar dudas directamente desde el flujo de trabajo.
