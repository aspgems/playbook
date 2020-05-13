# Configuración de proyectos rails con chamber

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

