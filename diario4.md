# GitHub Actions y YAML

Para hacer pipelines que automaticen algunos procesos de nuestro trabajo, vamos a usar GitHub Actions, sobre todo porque está integrada con nuestro proyecto y tiene mucho soporte de la comunidad.

> Mirarlo en GitHub Workflow.

Las pipelines se guardan en formato YAML ".github/workflows/", carpeta que habría que crear.

YAML es más simple que HTML y XML.

## Reglas básicas de YAML

YAML es sensible al espacio en blanco, así que la indentación importa.

### Pairs key-value

```yaml
language: Python
framework: Django
```

### La indentación solo acepta espacios, no tabs

```yaml
app:
    name: blog
    version: 1.0
```

Usar tabs produce error o comportamientos inesperados.

```yaml
app:
    name: blog
    version: 1.0
```

### Nested structure

```yaml
services:
    - name: database
        type: postgres
        version: 13
    - name: cache
        type: redis
        version: 6
```
### Scalars
pi: 3.14
is_active: true

###Folded Block
description: >
 This is a blog.
 It suppoets multiple lines.

### Literal Block
description:
 This is a blog.
 It suppoets multiple lines.

### Anchors
default: &default
 retries: 3
 timeout: 30s

### Aliases
service1:
 <<: *default
 url: http://service1.com

service2:
 <<: *default
 url: http://service2.com

## KEYWORDS Y SINTAX

### name
para definir el worflow/ job_title

### on
para definir triggers (push, Pr, Schedule)

### jobs
para definir jobs, sus OS y sus steps

### steps
la secuencia de acciones del job

### run
invoca la shell command para ejecutar

### uses
para hacer pre-build actions

### with
para pasar parametros a acciones

### env
establer variables de entorno

### needs
hacer un trabajo dependa de otro


---

crea repo en gh*

npm init -y     // me crea el package.json preconfigurado con un node project

Node.js permite poder porgramar el backend en javascript
npm (Node Package Manager) es el gestor de paquetes de Node.js

package.json -> es el documento de identidad de tu proyecto. 

Allí se guarda:
    El nombre y la versión de tu aplicación.
    La lista de librerías externas (dependencias) que tu proyecto necesita para funcionar.
    Scripts personalizados para arrancar o testear tu app.

Cree index.js y test.js, puse lo basico, y los añadi en scripts de node.js.