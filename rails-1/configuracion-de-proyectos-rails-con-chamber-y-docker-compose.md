# Configuración de proyectos rails con chamber y docker-compose

A la hora de configurar cualquier tipo de proyecto, independientemente de la tecnología, en ASPgems tenemos en cuenta los siguientes principios:

* La configuración inicial para arrancar el proyecto en local debe ser mínima
* Toda configuración debe de poderse sobreescribir sin alterar el espacio de trabajo de git
* Puesto que pasamos más tiempo trabajando en el entorno de desarrollo local, la configuración por defecto será la del entorno local
* La configuración en los demás entornos se sobreescribirá por medio de variables de entornos

Para conseguir esto, en rails, utilizamos chamber y docker-compose.

## Directorio settings <a id="directorio-settings"></a>

Los proyectos tienen un directorio `config/settings` donde viven los diferentes ficheros yaml con los valores que pueden cambiar entre entornos. Estos valores se utilizarán en otros ficheros de configuración típicos de rails como el de base de datos. Por ejemplo,

```text
# config/settings/postgres.yml

postgres:
  pool:     5
  username: postgres
  password: postgres
  host:     localhost
  database: my_postgres_database
```

```text
# config/database.yml

common: &default_settings
  adapter: postgresql
  encoding: unicode
  pool: <%= Chamber.env.postgres.pool %>
  username: <%= Chamber.env.postgres.username %>
  password: <%= Chamber.env.postgres.password  %>
  host:     <%= Chamber.env.postgres.host %>

development:
  <<: *default_settings
  database: <%= Chamber.env.postgres.database %>_development

test:
  <<: *default_settings
  database: <%= Chamber.env.postgres.database %>_test

production:
  <<: *default_settings
  database: <%= Chamber.env.postgres.database %>_production

```

De esta forma, podemos sobreescribir la configuración [por medio de ficheros](https://github.com/thekompanee/chamber/wiki/File-Based-Namespaces) \(por ejemplo `config/database-development.yml` o `config/database-production.yml`\) o [por medio de variables de entorno](https://github.com/thekompanee/chamber/wiki/Environment-Variables) `POSTGRES_DATABASE=my_postgres_database_production`, tal y como se explica en la documentación de Chamber.

La sobreescritura de la configuración por medio de ficheros, sólo se debe hacer para trabajar en local, puesto que seguimos los principios de las [12 factor apps](https://12factor.net/config), y todos los ficheros de configuración con el sufijo `-development`, `-production` o `-test` o `-local` estarán “gitignorados” para tener siempre el espacio de trabajo limpio.

```text
# .gitignore

/config/settings/**/*-development.*
/config/settings/**/*-test.*
/config/settings/**/*-production.*
```

## Configuración de servicios con docker compose

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

En caso de querer sobreescribir este fichero, contaremos con el fichero `docker-compose.override.yml`, que estará debidamente ignorado en git

```text
docker-compose.override.yml
```

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

