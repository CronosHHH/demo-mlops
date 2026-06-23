# Jun22

Se me pidio crear una Entidad de Servicio (SP - service principal) pero como estoy usando una cuenta de la universidad no me dejo.

> ## Diferencias: Service Principal vs Managed Identity
>
> | Aspecto | Service Principal | Managed Identity |
> |--------|------|----------|
> | **Credenciales** | Tú las generas y guardas | Azure las genera y rota |
> | **Secreto almacenado** | ⚠️ Alto riesgo de exposición | ✅ No hay secreto que robar |
> | **¿Dónde funciona?** | Cualquier lugar (portátil) | Solo dentro de Azure |
> | **Complejidad** | Moderada (debes gestionar) | Baja (Azure gestiona todo) |
> | **Renovación de credenciales** | Manual (cada X meses) | Automática |
> | **Costo** | Gratis | Gratis |
> | **Seguridad general** | Media (depende de ti) | Alta (Azure la cuida) |
> | **Caso de uso ideal** | Acceso desde fuera de Azure | Pipelines dentro de Azure (CI/CD, GitHub Actions) |

La solución: 
Creamos una Identidad Administrada (Managed Identity):
    mi-devops-conexion
directamente en tu panel de Azure, otorgándole el rol de Colaborador (Contributor) dentro del control de acceso (IAM) de tu nuevo espacio de trabajo de Machine Learning. 

Después, regresamos a Azure DevOps, cambiamos el tipo de identidad a Managed Identity para enlazar el recurso que habías creado y finalizamos la configuración guardando la conexión con el nombre:
    aml-workspace-connection-demo
, dejando marcada la casilla de seguridad para dar acceso libre a todos tus pipelines de despliegue.

# Jun 23

Hicimos la integración entre GitHub y Azure Machine Learning usando Managed Identity y Federated Credentials.

> **Federated Credentials**
> 
> Un mecanismo de confianza sin contraseña entre GitHub y Azure. En lugar de guardar secretos, GitHub y Azure se verifican mutuamente mediante tokens.
> 
> **En Azure Portal:**
> - Abriste tu Managed Identity: `mi-devops-conexion`
> - Fuiste a **Settings** → **Federated credentials**
> - Clickeaste **Add credential** y rellenaste:
>   - Escenario: GitHub Actions
>   - Organización: Tu usuario de GitHub
>   - Repositorio: demo-mlops
>   - Rama: main

Luego extraímos los valores necesarios de tu Managed Identity (mi-devops-conexion) del JSON que obtuviste en Azure Portal: Client ID, Tenant ID, Subscription ID y Resource Group. Creamos 5 secretos en GitHub para almacenar estas credenciales de forma segura.

Después configuramos un workflow de GitHub Actions en train.yml que automáticamente ejecuta trabajos de entrenamiento en Azure ML cuando haces push a la rama main.

El resultado final: cada vez que hagas push a GitHub, automáticamente se ejecutará un job de entrenamiento en tu workspace de Azure Machine Learning sin necesidad de guardar contraseñas, todo basado en confianza verificada entre plataformas.