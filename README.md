---
description: 'Todos los proyectos en ASPgems deberían tener, por defecto, lo siguiente:'
---

# Checklist de proyectos

## Cuentas y servicios de terceros

_Todos los servicios que se utilicen estarán debajo de la cuenta del cliente._

* Un repositorio en [gitlab](http://gitlab.com/)
* Una cuenta en [rollbar](http://rollbar.com/) para la gestión de bugs
* Una cuenta asociada a [pingdom](https://www.pingdom.com/)

## DevOps

* Un Pipeline
* Un entorno de staging
* Un entorno de producción
* Un sistema de monitorización de la app y de la infraestructura \(APM tipo newrelic\)
* Acceso a logs

## Seguridad

* Acceso protegido al entorno de staging utilizando el sistema de cognito
* Un usuario \(al menos\) del cliente que pueda acceder al entorno de staging
* Un mecanismo para la gestión segura de secretos
* Certificado SSL

## Comunicación y notificaciones

* Una sala en slack con, al menos, el nombre del repo en el topic
* Notificaciones de rollbar integradas en esta sala
* Notificaciones del pipeline roto \(al menos master\) en la sala

## Desarrollo

Utilidades para el desarrollo en local de la aplicación

* Instrucciones de instalación
* Instalación automática
* Generación automática de datos de prueba
* Importación segura de datos de otros entornos \(producción, staging...\)

## Testing

El proyecto tiene que estar preparado para añadir tests

* Se pueden añadir tests unitarios
* Se pueden añadir tests de aceptación
* Se pueden añadir tests de aceptación con javascript

## Datos

* Backups
* Ofuscación de datos
* Migración de datos

## README.md

Todos los proyectos deberían tener un README versionado en el proyecto en el que se encuentre este mismo checklist. Puedes copiar el siguiente código y copiarlo en tu README.md

```text
# Checklist
- [x] This README.md

## Services
- [ ] Repo: [your url here](http:s//gitlab.com/client/project)
- [ ] Rollbar account: [Your rollbar account](https://rollbar.com/client/project)
- [ ] Pingdom account: [Your pingdom account](https://www.pingdom.com/)

## Devops
- [ ] Monitoring system / APM: [Your APM here](https://example.com)
- [ ] Pipeline: [A pipeline](https://app.gitbook.com/@aspgems/s/playbook/rails-1/pipeline-basico)
- [ ] Staging environment: [Your staging environmente here](https://example.com)
- [ ] Production environment: [Your production environment here](https://example.com)
- [ ] Logs in production

## Security
- [ ] Restricted access to staging
- [ ] Client user who can access to staging
- [ ] Secrets management
- [ ] SSL certificate

## Notifications and communication
- [ ] Slack room: #your_slack_room
- [ ] Notifications from rollbar in slack
- [ ] Notifications from broken pipeline in slack

## Development
- [ ] Automated installation
- [ ] Example data for development
- [ ] Can import data from production

## Testing
- [ ] Ready to unit testing
- [ ] Ready to acceptance test
- [ ] Ready to acceptance test with js

## Data
- [ ] Backups
- [ ] Data ofuscation
- [ ] Data migration strategy

## Log access
Describe how to access to logs in production

# Development

## Installation

## Data import from other environments

## Example data for development

# Secrets

# Data

## Backups

## Data ofuscation

## Data migration
```

