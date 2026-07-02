# Kubernetes (k8s)

## Indice

- [Idea general](#idea-general)
- [Términos clave](#terminos-clave)
- [Flujo del proyecto](#flujo-del-proyecto)
- [Archivos usados](#archivos-usados)
- [Referencias externas](#referencias-externas)

## Idea general

Kubernetes sirve para programar, escalar, autocurar y balancear contenedores entre varias máquinas.

Cuando muchos usuarios piden un único contenedor, ese contenedor puede saturarse. Si la aplicación depende solo de una instancia, la caída de ese contenedor puede dejar la app fuera de servicio. Kubernetes resuelve eso distribuyendo la carga, manteniendo réplicas y reiniciando lo que falle.

### Qué permite hacer Kubernetes

- Ejecutar la aplicación en varios nodos.
- Mantener réplicas según la demanda.
- Reiniciar contenedores fallidos o no saludables.
- Distribuir el tráfico entre varias copias de la app.
- Iniciar y parar contenedores.
- Hacer seguimiento de IPs internas.
- Escalar de forma horizontal o vertical.

## Términos clave

### Cluster

Un cluster es un grupo de máquinas físicas o virtuales que trabajan como un solo sistema.

- **Control Plane**: coordina el cluster. Aquí viven componentes como `API server` y `Scheduler`.
	- `API server`: recibe las peticiones de Kubernetes y expone la interfaz principal para crear, leer, actualizar y borrar recursos.
	- `Scheduler`: decide en qué nodo debe ejecutarse cada Pod según la carga y los recursos disponibles.
- **Worker Nodes**: son las máquinas donde se ejecutan los pods.

Cada Worker Node suele incluir:

- `kubelet`: agente que se comunica con el Control Plane.
- Runtime de contenedores: por ejemplo Docker.
- `kube-proxy`: gestiona red y enrutamiento dentro del cluster.

### Pod

Kubernetes no ejecuta contenedores directamente: los envuelve en un **Pod**.

- Es la unidad más pequeña que se puede desplegar.
- Lo normal es que un Pod contenga un solo contenedor.
- Cada Pod tiene su propia IP.
- Los Pods son efímeros: si se recrean, pueden recibir otra IP.

### ReplicaSet

Un ReplicaSet mantiene siempre un número concreto de Pods activos.

- Garantiza que haya varias copias de la app corriendo.
- Si un pod cae, crea otro para reemplazarlo.

### Deployment

Un Deployment está por encima del ReplicaSet y define cómo se actualiza la aplicación.

- Permite despliegues controlados.
- Facilita `rolling updates`, es decir, actualizaciones graduales.
- Es la forma habitual de gestionar versiones de una app en Kubernetes.

### Service

Un Service da un endpoint estable para acceder a los Pods.

- Interconecta Pods con IPs dinámicas.
- Expone la aplicación con una IP o nombre DNS estable.
- Puede balancear el tráfico entre varias réplicas.

### ConfigMap y Secrets

Kubernetes permite pasar configuración a la aplicación sin meterla dentro de la imagen.

- **ConfigMap**: guarda datos de configuración no sensibles.
- **Secret**: guarda datos sensibles como contraseñas o tokens.

### Ingress

Un Ingress es un enrutador HTTP/HTTPS para exponer servicios al exterior.

- Actúa como puerta de entrada para los usuarios.
- Permite mapear rutas a distintos servicios.

### Volumes

Los contenedores y Pods son efímeros, así que su información interna puede perderse.

- Los volumes permiten almacenamiento persistente.
- Sirven para conservar datos fuera del ciclo de vida del contenedor.

## Flujo del proyecto

### Cluster local con Minikube

Vamos a usar Minikube para crear un cluster local de un solo nodo.

En este escenario todo vive en la misma máquina: Control Plane y Worker Node.

### Instalación de herramientas

```bash
npm init -y
npm install express
```

Para instalar `kubectl`:

```bash
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```

Para instalar Minikube:

```bash
curl -LO https://github.com/kubernetes/minikube/releases/latest/download/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube && rm minikube-linux-amd64
```

### index.js

Se crea un `index.js` con Express para comprobar que la app funciona dentro del cluster.

Lo esencial que hace es:

- crea la app con `express()`.
- lee el puerto desde `process.env.PORT` y usa `3000` si no existe.
- responde en `/` con un JSON que incluye el nombre del servicio, el `POD_NAME` y la hora actual.
- expone `/readyz` para readiness.
- expone `/healthz` para healthcheck.
- arranca el servidor con `app.listen(...)`.

### package.json

Se añaden scripts en `package.json`:

```json
"dev": "node --watch index.js"
"start": "node index.js"
```

- `dev` usa `--watch` para reiniciar automáticamente cuando cambia el archivo.
- `start` arranca la app sin observar cambios, que es lo normal para despliegues.

> Ojo: `--watch` requiere una versión moderna de Node.

### Dockerfile y compose

Se usan para contenerizar la app y ejecutarla localmente.

- `Dockerfile`: define cómo se construye la imagen.
- `docker-compose.yaml`: levanta la app con sus puertos y volúmenes.
- `.dockerignore` y `.gitignore`: evitan copiar o versionar archivos innecesarios.

Comando de arranque local:

```bash
docker compose up --build
```

Eso construye la imagen y deja corriendo el servidor dentro del contenedor.

### Publicar la imagen en Docker Hub

Antes de desplegar en Kubernetes, la imagen se publica en Docker Hub.

```bash
docker tag hello-kubernetes-api:latest renatorrvv/hello-kubernetes-api:latest
docker push renatorrvv/hello-kubernetes-api:latest
```

- `docker tag` crea una etiqueta para la imagen local.
- `docker push` sube la imagen al registry.

### Manifiestos de Kubernetes

Se crean los archivos `deployment.yaml` y `service.yaml` para hacer la app escalable.

- `deployment.yaml`: define la imagen, las réplicas, los probes y las variables de entorno.
- `service.yaml`: expone los Pods con un servicio estable.

Despliegue:

```bash
kubectl apply -f k8s/deployment.yaml
kubectl apply -f k8s/service.yaml
```

Comandos útiles:

```bash
kubectl get nodes
kubectl cluster-info
kubectl get pods -w
kubectl get services
minikube service hello-kubernetes-api-service
```

### Qué pasa por detrás

- El API server recibe los YAML.
- El Scheduler decide en qué nodo va cada Pod.
- El kubelet del nodo crea y arranca el Pod.
- El Service enruta el tráfico hacia los Pods.
- Si hay más de un Pod, el Service balancea la carga.

### Script de despliegue

Para no repetir todos los comandos a mano, se crea `deploy.sh`.

Ese script hace, en orden:

- construir la imagen.
- subirla a Docker Hub.
- aplicar los manifiestos.
- listar Pods y Services.
- mostrar el servicio principal.

Luego se añade al `package.json`:

```json
"deploy": "sh deploy.sh"
```

Así se puede ejecutar todo con:

```bash
npm run deploy
```

## Archivos usados

### hello-kubernetes

- [index.js](hello-kubernetes/index.js)
- [package.json](hello-kubernetes/package.json)
- [deploy.sh](hello-kubernetes/deploy.sh)
- [Dockerfile](hello-kubernetes/Dockerfile)
- [docker-compose.yaml](hello-kubernetes/docker-compose.yaml)
- [k8s/deployment.yaml](hello-kubernetes/k8s/deployment.yaml)
- [k8s/service.yaml](hello-kubernetes/k8s/service.yaml)


## Referencias externas

- [Docker Compose](https://docs.docker.com/compose/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [kubectl docs](https://kubernetes.io/docs/reference/kubectl/)
- [Kubernetes basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
- [Minikube](https://minikube.sigs.k8s.io/docs/)
