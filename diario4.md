# GitHub Actions y YAML — notas rápidas

Pequeña guía para crear pipelines con GitHub Actions y recordar reglas básicas de YAML.

> Los workflows de GitHub Actions se guardan en `.github/workflows/` y usan archivos YAML.

## ¿Por qué GitHub Actions?

- Integrado con el repositorio.
- Mucho soporte de la comunidad y marketplace de acciones.
- Útil para CI (tests) y CD (deploys).

---

## Reglas básicas de YAML

- YAML es sensible al espacio en blanco: la indentación importa y solo acepta espacios (no tabs).
- Usa 2 espacios por nivel para mantener consistencia.

### Ejemplo: pares clave-valor

```yaml
language: Python
framework: Django
```

### Indentación correcta (no usar tabs)

```yaml
app:
    name: blog
    version: 1.0
```

### Estructuras anidadas

```yaml
services:
    - name: database
        type: postgres
        version: 13
    - name: cache
        type: redis
        version: 6
```

### Tipos básicos (scalars)

```yaml
pi: 3.14
is_active: true
```

### Bloques plegados y literales

```yaml
# Folded block (convierte saltos de línea en espacios)
description: >
    This is a blog.
    It supports multiple lines.

# Literal block (preserva saltos de línea)
description: |
    This is a blog.
    It supports multiple lines.
```

### Anchors y aliases

```yaml
default: &default
    retries: 3
    timeout: 30s

service1:
    <<: *default
    url: http://service1.com

service2:
    <<: *default
    url: http://service2.com
```

---

## Keywords comunes en workflows de GitHub Actions

- `name`: nombre del workflow.
- `on`: define los triggers (`push`, `pull_request`, `schedule`, `workflow_dispatch`, ...).
- `jobs`: define bloques de ejecución, OS y `steps`.
- `steps`: secuencia de acciones dentro de un job.
- `run`: ejecuta un comando en la shell.
- `uses`: usa una acción preconstruida (desde marketplace o repo).
- `with`: parámetros para una acción.
- `env`: variables de entorno.
- `needs`: declarar dependencias entre jobs.

---

## Comandos útiles: Node.js / npm

Inicializar un proyecto Node.js rápidamente:

```bash
npm init -y
```

Explicación: `npm init -y` crea un `package.json` con valores por defecto sin pedir interacción. Es la base para instalar dependencias y definir scripts.

Breve descripción de `package.json`:

- `name` y `version` del proyecto.
- `dependencies` y `devDependencies`.
- `scripts`: comandos como `start`, `test`, `build`.

Ejemplo: añadir scripts básicos

```json
{
    "scripts": {
        "start": "node index.js",
        "test": "node test.js"
    }
}
```

---

## Notas finales

- Node.js permite programar el backend en JavaScript.
- `npm` (Node Package Manager) gestiona paquetes y scripts.
- Creé `index.js` y `test.js` y los añadí como scripts en `package.json`.
- Ahora al hacer git push, corre la pipeline
- mirar el la parte de action en el gh marketplace