# IaC - Infrastructure as Code

## Idea general

IaC significa escribir la infraestructura como archivos de texto.

Eso permite que la configuración sea:

- fácil de versionar
- fácil de auditar
- fácil de reproducir
- fácil de compartir

La idea es evitar configuraciones manuales y dispersas, y reemplazarlas por definiciones claras y repetibles.

## Beneficios

- Control de versiones
- Testeabilidad
- Reproducibilidad
- Colaboración

## Ejemplo

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t2.micro"
  count         = 3
}
```

### Explicación del ejemplo

| Elemento | Significado |
| --- | --- |
| `resource` | Tipo de recurso que se va a crear. |
| `"aws_instance"` | Nombre del recurso dentro de Terraform. |
| `ami` | ID de la imagen base que se usará. |
| `instance_type` | Tipo de instancia a crear. |
| `count` | Número de instancias que se van a levantar. |

## Tecnologías

Existen distintas herramientas de IaC según el proveedor o el enfoque:

| Herramienta | Enfoque |
| --- | --- |
| AWS CloudFormation | Específica de AWS |
| Azure Resource Manager | Específica de Microsoft Azure |
| Google Cloud Deployment Manager | Específica de GCP |
| Terraform | Cloud-agnostic, funciona con varios proveedores |

## Terraform

Terraform usa HCL, que significa HashiCorp Configuration Language.

Ventajas principales:

- es legible
- es fácil de escribir
- permite desplegar en AWS, Azure, GCP y otros entornos

Ejemplos de uso:

- crear un cluster de Kubernetes en AWS
- crear una base de datos en Azure
- crear almacenamiento en GCP

## Helm

Helm es el gestor de paquetes de Kubernetes.

Su función es empaquetar aplicaciones en **charts**, que son colecciones de archivos que describen los recursos necesarios para desplegar la aplicación.

En la práctica, Helm convierte YAML sueltos y repetitivos en plantillas configurables.

## Sugerencia de aprendizaje

Lo recomendable es aprender primero:

1. Terraform
2. Helm
3. Luego, si hace falta, una herramienta específica del proveedor cloud que vayas a usar


