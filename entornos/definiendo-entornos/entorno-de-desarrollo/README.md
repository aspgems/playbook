# Entorno de desarrollo

La función de este entorno es la de permitir que los desarrolladores realicen su trabajo de forma eficiente y con el menor número de trabas posible.

Cada desarrollador es responsable de su propio entorno por lo que debería tener una forma lo más sencilla posible de arrancarlo en modo desarrollo con la mínima configuración e instalación posibles. Para esto seguimos el principio de convencion sobre configuración, de forma que el proyecto es instalable y ejecutable con un mínimo esfuerzo, pero los desarrolladores pueden modificarlo a su gusto sin trabas.

## Instalación y ejecución

* Para poder arrancar con el entorno no tiene que ser necesario modificar ningún fichero versionado
* El proyecto no debería depender de ningún otro proyecto instalado localmente y deberíamos utilizar servicios mockeados o en contenedores en su lugar

## Infraestructura

La infraestructura por defecto del entorno en local se hará con docker-compose tal y como se describe [aquí](https://aspgems.gitbook.io/playbook/entornos/definiendo-entornos/entorno-de-desarrollo/docker-y-docker-compose). Los proyectos tendrán por defecto:

* Un docker-compose con los servicios de terceros necesarios para el desarrollo \(bases de datos, sistemas de colas, etc.\)
* Estará ignorado el fichero `docker-compose.override.yml` para permitir que cada desarrollador extienda la infraestructura básica a su antojo \(por ejemplo, añadiendo la propia aplicación como un servicio más\)
* Si el equipo así lo ve conveniente puede haber diferentes extensiones para arrancar en local el proyecto en diferentes modos \(por ejemplo, `docker-compose.production.yml` o `docker-compose.test.yml`

## Configuración y secretos

* Seguimos el principio de convención sobre configuración de forma que, salvo en casos excepcionales \(por ejemplo, secretos\) no haga falta configurar nada para que el proyecto arranque en modo desarrollo
* Por defecto todos los servicios deberían apuntar a localhost
* Tiene que haber una forma sencilla de sobreescribir sólo aquellas configuraciones que queramos cambiar. Por ejemplo mediante variables de entorno o ficheros de configuración previamente ignorados del repositorio
* Debería minimizarse el uso de secretos en local

Un ejemplo de esto es [el uso de chamber en rails](https://aspgems.gitbook.io/playbook/rails-1/configuracion-de-proyectos-rails-con-chamber-y-docker-compose) 

## Datos

Hay tres estrategias posibles para el uso de datos en local

* Cada desarrollador es encargado de mantener su propio set de datos
* Los desarrolladores comparten un conjunto de datos conocido que hay que mantener como parte del código \(mediante el uso de scripts, factorías, fixtures...\)
* Usar una copia anonimizada de los datos de producción

Cada equipo de desarrollo es responsable de elegir la estrategia que mejor se ajuste al proyecto



