# Entorno de desarrollo

La función de este entorno es la de permitir que los desarrolladores realicen su trabajo de forma eficiente y con el menor número de trabas posible.

Cada desarrollador es responsable de su propio entorno por lo que debería tener una forma lo más sencilla posible de arrancarlo en modo desarrollo con la mínima configuración e instalación posibles. Para esto seguimos el principio de convencion sobre configuración, de forma que el proyecto es instalable y ejecutable con un mínimo esfuerzo, pero los desarrolladores pueden modificarlo a su gusto sin trabas.

## Instalación y ejecución

* Para poder arrancar con el entorno no tiene que ser necesario modificar ningún fichero versionado
* El proyecto no debería depender de ningún otro proyecto instalado localmente y deberíamos utilizar servicios mockeados o en contenedores en su lugar

## Infraestructura

La infraestructura por defecto del entorno en local se hará con docker-compose. Los proyectos tendrán por defecto:

* Un docker-compose con los servicios de terceros necesarios para el desarrollo \(bases de datos, sistemas de colas, etc.\)
* Estará ignorado el fichero `docker-compose.override.yml` para permitir que cada desarrollador extienda la infraestructura básica a su antojo \(por ejemplo, añadiendo la propia aplicación como un servicio más\)
* Si el equipo así lo ve conveniente puede haber diferentes extensiones para arrancar en local el proyecto en diferentes modos \(por ejemplo, `docker-compose.production.yml` o `docker-compose.test.yml`
* Cada desarrollador podrá tener a su disposición el fichero `Dockerfile.local` que estará ignorado en `git`

### Imágenes docker para desarrollo

El proyecto podrá tener, **de forma opcional**, una imagen oficial para desarrollo. Esta imagen se podrá crear mediante el uso de un Dockerfile utilizando la funcionalidad [multistage build](https://docs.docker.com/develop/develop-images/multistage-build/) de docker o utilizando un Dockerfile específico para ello.

En todo caso el fichero `Dockerfile.local` estará ignorado en el `.gitignore`

```text
# .gitignore
Dockerfile.local
```

### Configuración de servicios con docker compose

Para la configuración de servicios \(postgres, redis, mailcatcher…\) tenemos disponible un fichero `docker-compose.yml` por proyecto, de forma que baste con ejecutar `docker-compose up -d` para levantar los servicios necesarios. Por ejemplo:

```text
# docker-compose.yml

version: '3'
services:
  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres:/var/lib/postgresql/data
  redisdb:
    image: redis
    command: redis-server --appendonly yes
    ports:
      - "6379:6379"
    volumes:
      - redis:/data

  memcached:
    image: memcached
    ports:
      - "11211:11211"

  mailcatcher:
    image: tophfr/mailcatcher
    ports:
      - "1080:80"
      - "25:25"

volumes:
  redis: {}
  postgres: {}
```

Como el uso de docker-compose es opcional todos los servicios estarán mapeados de forma que se accederá a ellos a través de localhost por defecto. De esta forma no hará falta modificar ninguna configuración si alguien decide no utilizar docker.

### Extendiendo la configuración básica

En caso de querer sobreescribir este fichero, haremos [uso de varios ficheros ficheros de docker-compose](https://docs.docker.com/compose/extends/#multiple-compose-files).

#### docker-compose.override.yml

Para extender nuestra configuración en local de forma fácil contaremos con el fichero `docker-compose.override.yml`, que estará debidamente ignorado en git

```text
# .gitignore

docker-compose.override.yml
```

```text
# docker-compose.override.yml

version: '3'
services:
  app:
    build:
     context: .
     dockerfile: Dockerfile
    ports:
     - "3000:3000"
    depends_on:
     - pg
    labels:
     traefik.enable: "true"
     traefik.http.routers.app.rule: "Host(`whoami.localhost`)"
     traefik.http.routers.whoami.entrypoints: web
```

#### Compartiendo extensiones comunes \(opcional\)

Si el equipo lo cree conveniente se podrán añadir diferentes ficheros para simular un entorno de producción o de test en local. Por ejemplo:

```text
# docker-compose.production.yml

version: '3'
services:
  app:
    build:
      context: .
      args:
        rails_master_key: XXXXXXXXXXXXXXXXXXXX
    image: spreedemo:local
    depends_on:
      - postgres
    ports:
      - "3000:3000"
    environment:
      POSTGRES_USERNAME: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_HOST: postgres
      RAILS_SERVE_STATIC_FILES: "true"

  postgres:
    image: postgres
    ports:
      - "5432:5432"
    volumes:
      - postgres:/var/lib/postgresql/data
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres

volumes:
  postgres: {}
```

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



