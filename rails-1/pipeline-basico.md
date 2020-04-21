---
description: (Con gitlab)
---

# Pipeline Básico

Cuando comenzamos un proyecto, o añadimos por primera vez un pipeline a un proyecto en marcha, deberíamos incluir las siguientes tareas básicas

* Linter \(con rubocop\)
* Análisis de seguridad con brakeman
* Análisis de seguridad de las gemas
* Análisis de gemas desactualizadas

A la hora de ejecutar estas gemas en el servidor de CI \(gitlab en nuestro caso\), no usaremos las del Gemfile, sino que las instalaremos directamente en la imagen que utilicemos. Esto es así por dos razones principales:

* Así nos aseguramos de que utilizamos las versiones más actualizadas de estas gemas
* Hace más fácil la creación del pipeline

## Imagen base

La imagen base será la imagen oficial de ruby que corresponda a la versión de ruby que utilice nuestra aplicación.

```text
image: "ruby:2.6.4"
```

## Rubocop

Se instalará y se ejecutará rubocop en su propia tarea:

```text
rubocop:
  script:
    - gem install rubocop
    - rubocop
```

Para que esta tarea pase correctamente el pipeline seguramente haya que lidiar con ofensas existentes en el código. La forma más sencilla de conseguir esto es ignorar todas las ofensas actuales mediante la generación del fichero `.rubocop_todo.yml` y su inclusión en el repositorio.

De esta forma nos aseguramos que no añadimos nuevas ofensas en futuros desarrollos, mientras vamos poco a poco resolviéndolas.

Para esto basta con ejecutar el comando

```text
rubocop --auto-gen-config
```

Más información en la [documentación de rubocop](https://github.com/rubocop-hq/rubocop/blob/master/manual/index.md)

## Brakeman

Se instala y se ejecuta brakeman en su propia tarea

```text
brakeman:
  script:
    - gem install brakeman
    - brakeman -z
```

Al igual que con rubocop, para que esta tarea pase el pipeline correctamente hace falta ignorar y documentar las actuales ofensas.

Para esto hace falta utilizar la consola interactiva de la gema. Esto generará un fichero de configuración que habrá que incluir en el repositorio.

Para ejecutar la consola interativa hay que ejecutar

```text
brakeman -I
```

Para más información consultar [la documentación de brakeman](https://brakemanscanner.org/docs/options/)

## Bundle audit

Bundle audit se instalará y se ejecutará en su propia tarea:

```text
bundle audit:
  script:
    - gem install bundler-audit
    - bundle audit check --update
```

Al igual que con rubocop y con brakeman si existieran vulnerabilidades actuales y con el fin de no añadir nuevas, habría que ignorarlas.

Sin embargo la gema bundle-audit no tiene una opción para hacer esto y hay que hacerlo a mano.

Si esto fuera necesario habría, primero que generar el fichero de gemas a ignorar a mano \(llamado `.bundle_audit_config`\), versionarlo y cambiar la tarea de bundler-audit por la siguiente:

```text
bundle audit:
  script:
    - gem install bundler-audit
    - bundle audit check --update --ignore $(cat .bundler_audit_todo)
```

Un ejemplo del fichero `.bundle_audit_todo` sería el siguiente:

```text
CVE-2018-7212
CVE-2018-3769
CVE-2018-1000201
CVE-2019-8331
CVE-2016-10735
CVE-2019-5421
```

## Bundle outdated

Para asegurarnos de que tenemos las gemas más o menos al día, necesitamos crear una tarea que nos compruebe si tenemos alguna gema desactualizada.

Como no queremos que el hecho de tener una gema desactualizada nos rompa el pipeline \(hay veces que la incompatibilidad de versiones entre gema hace imposible estar a la última\), esta tarea se ejecutará de forma planificada y permitirá que falle.

```text
bundle outdated:
  only:
    - schedules
  script:
    - bundle outdated
  allow_failure: true
```

Para esto hay que configurar un [schedule en gitlab](https://docs.gitlab.com/ee/user/project/pipelines/schedules.html)

## Ejemplo de pipeline mínimo

```text
image: "ruby:2.6.4"

rubocop:
  script:
    - gem install rubocop
    - rubocop

brakeman:
  script:
    - gem install brakeman
    - brakeman -z

bundle audit:
  script:
    - gem install bundler-audit
    - bundle install --without development
    - bundle audit check --update

bundle outdated:
  only:
    - schedules
  script:
    - bundle outdated
  allow_failure: true
```

