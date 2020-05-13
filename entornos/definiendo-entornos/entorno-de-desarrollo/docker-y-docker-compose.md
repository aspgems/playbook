# Docker y docker-compose

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

