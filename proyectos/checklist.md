---
description: 'Todos los proyectos en ASPgems deberían tener, por defecto, lo siguiente:'
---

# Checklist



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

