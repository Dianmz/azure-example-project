# Despliegue de aplicación NodeJS en AKS utilizando Azure DevOps Pipelines

## Cloud Native Guatemala 🇬🇹

### Despliegues

Los despliegues automatizados se refieren al proceso de implementación de software de forma automática y sistemática, sin intervención manual significativa. Estos despliegues automatizados son esenciales en el desarrollo de software moderno por varias razones:

- Rapidez y Eficiencia
- Consistencia
- Reducción de Errores
- Escalabilidad
- Facilita la Integración Continua y la Entrega Continua (CI/CD)
- Mejora la Colaboración

En resumen, los despliegues automatizados son esenciales para acelerar el ciclo de vida del desarrollo de software, mejorar la calidad del software y permitir a las organizaciones adaptarse rápidamente a las demandas del mercado.

### Desafíos de los Despliegues

La implementación y gestión de aplicaciones en la nube presentan una serie de desafíos comunes que deben abordarse para garantizar el éxito y la eficiencia en el uso de los recursos en la nube. Algunos de estos desafíos incluyen

- **Seguridad y cumplimiento**: Proteger los datos en tránsito y en reposo, implementar controles de acceso adecuados y cumplir con regulaciones específicas de la industria.
- **Gestión de costos**: El uso ineficiente de los recursos en la nube puede dar lugar a costos inesperadamente altos. Es importante optimizar el uso de recursos para evitar gastos innecesarios y garantizar que los recursos se escalen según la demanda.
- **Escalabilidad y rendimiento**: Asegurar que las aplicaciones puedan escalar de manera eficiente para manejar picos de tráfico y cargas de trabajo variables es crucial para garantizar un rendimiento óptimo. Esto implica diseñar arquitecturas escalables y utilizar servicios en la nube que permitan la escalabilidad automática.
- **Disponibilidad y tolerancia a fallos**: Las aplicaciones en la nube deben estar diseñadas para ser altamente disponibles y tolerantes a fallos. Esto implica utilizar arquitecturas distribuidas, replicación de datos y servicios de respaldo para garantizar la disponibilidad continua de la aplicación, incluso en caso de fallos de hardware o software.
- **Gestión de la complejidad**: La adopción de servicios en la nube puede aumentar la complejidad de la infraestructura y la gestión de la aplicación. Es importante implementar herramientas de gestión adecuadas y adoptar prácticas de DevOps para automatizar tareas de implementación, monitoreo y mantenimiento.
- **Interoperabilidad y portabilidad**: Garantizar la interoperabilidad entre diferentes servicios en la nube y la portabilidad de las aplicaciones entre diferentes proveedores de servicios en la nube puede ser un desafío. Esto implica utilizar estándares abiertos y adoptar arquitecturas y herramientas que faciliten la portabilidad de las aplicaciones.
- **Gestión de datos**: Gestionar grandes volúmenes de datos en la nube puede ser complejo, especialmente en términos de almacenamiento, acceso y análisis de datos. Es importante diseñar estrategias de gestión de datos efectivas y utilizar servicios en la nube que faciliten el almacenamiento, procesamiento y análisis de datos a escala.

### Azure DevOps

[Azure DevOps](https://azure.microsoft.com/es-es/products/devops) es una plataforma integral de desarrollo de software proporcionada por Microsoft que ofrece un conjunto de herramientas y servicios para planificar, desarrollar, probar y desplegar aplicaciones de manera eficiente. Anteriormente conocida como Visual Studio Team Services (VSTS), Azure DevOps consta de varios componentes principales que se integran de manera fluida para respaldar el ciclo de vida completo del desarrollo de software. Estos componentes incluyen:

- Azure Boards
- Azure Repos
- Azure Pipelines
- Azure Test Plans
- Azure Artifacts
- Azure DevOps Analytics

### Kubernetes

Kubernetes es una plataforma de código abierto diseñada para automatizar, escalar y gestionar aplicaciones en contenedores. Fue desarrollada originalmente por Google y ahora es mantenido por la Cloud Native Computing Foundation (CNCF). Kubernetes simplifica el despliegue, la gestión y la escalabilidad de aplicaciones contenerizadas, permitiendo a los desarrolladores y administradores de sistemas implementar aplicaciones de manera consistente en entornos de nube o locales.

### Azure Kubernetes Service (AKS)

[AKS](https://azure.microsoft.com/es-es/products/kubernetes-service) es un servicio de computación en la nube proporcionado por Microsoft Azure. AKS permite a los usuarios implementar, administrar y escalar fácilmente contenedores utilizando Kubernetes. Azure Kubernetes Service simplifica la administración de clústeres de Kubernetes al encargarse de tareas como la implementación de nodos, la configuración de redes, la escalabilidad automática y la actualización del software de Kubernetes, permitiendo a los equipos de desarrollo centrarse en el desarrollo de aplicaciones sin preocuparse por la infraestructura subyacente.

### Integración de Azure DevOps con Kubernetes

Integrar Azure DevOps con Kubernetes permite automatizar el proceso de implementación de aplicaciones en contenedores en clústeres de Kubernetes. Aquí hay una guía general sobre cómo lograr esta integración:

#### Requisitos

- Una cuenta de Azure con una suscripción activa.
- Una conexión del servicio Azure Resource Manager.
- Una cuenta de GitHub

#### Configuración de un clúster de Kubernetes en Azure

Antes de integrar Azure DevOps con Kubernetes, es necesario tener un clúster de Kubernetes configurado en Azure. Se puede crear un clúster de Kubernetes usando Azure Kubernetes Service (AKS), que simplifica la administración del clúster.

#### Creación de un Registro de Contenedor

```
# Crear un resource group
az group create --name myapp-rg --location eastus

# Crear un container registry
az acr create --resource-group myapp-rg --name mycontainerregistry --sku Basic

# Crear un Clúster de Kubernetes
az aks create \
    --resource-group myapp-rg \
    --name myapp \
    --node-count 1 \
    --enable-addons monitoring \
    --generate-ssh-keys
```

#### Creación del Pipeline

1. En el dashboard de Azure DevOps ir a la sección de Pipelines y seleccionar **New Pipeline**.
2. Seleccionar la fuente del código (GitHub, Azure Repos, etc.).
3. Seleccionar la opción de **Implementar en Azure Kubernetes Service**
4. Seleccionar la suscipcion en la que se creó el registro y el cluster. Llenar todos los campos con la información del clúster creado.
5. En éste ejemplo se utilizó el puerto de servicio **8080**
6. Habilitar las casilla **Enable Review App for Pull Request**
7. Seleccionar **Validar y Configurar**

Este proceso realizará lo siguiente:

- Crear una conexión de servicio del registro de Docker para permitir que el pipeline pueda insertar imágenes en el registro de contenedor.

- Creará un entorno y un recurso de Kubernetes dentro.

- Generará un archivo azure-pipelines.yml, que define el pipeline.

- Generará los archivos de manifiesto de Kubernetes. Estos archivos se generan mediante las plantillas deployment.yml y service.yml en función de las selecciones realizadas.

### Demo

Este código de ejemplo utiliza el Framework Web de Node.js Express para crear un servidor web básico que recube peticiones HTTP en el puerto 8080.

Éste proyecto incluye un Dockerfile en `app/Dockerfile`, en el cuál se definen los pasos para construir la imagen que permitirá al contenedor de Node.js correr. También se incluye el manifiesto de despliegue `deployment.yml` y su correspondiente `service.yml`. `deployment.yml` Describe como hacer el deploy de una aplicación Node.JS contenelizada hacia un clúster de Kubernetes. `service.yml` crea un servicio y un secret resource.


## Presentación

[Presentación](https://docs.google.com/presentation/d/1EiNQRqnHPBdwD4ssTLVBaO7Q1qDy3NvOD2ilG0IBnj4/edit?usp=sharing)