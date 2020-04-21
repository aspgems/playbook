# Factorías

### Uso de factorías <a id="uso-de-factor&#xED;as"></a>

Las factorías en ASPgems las utilizamos para:

* Testing
* Generar datos fake en entornos de prueba \(local, reviews, staging…\)

### Reglas <a id="reglas"></a>

* Usamos la gema [factory\_bot](https://github.com/thoughtbot/factory_bot) para definir factorías
* Todos los modelos deberían tener una factoría mínima
* Todas las factorías tienen que generar objetos válidos
* En nuestro pipeline utilizaremos el [lint](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#linting-factories) que provee `factory_bot` para testear las factorías antes que cualquier otra cosa
* Para los valores de las factorías usaremos la gema [faker](https://github.com/stympy/faker)
* A la hora de crear objetos, la estrategia que utilizaremos, por orden de preferencia será:
  1. `build`
  2. `stub`
  3. `create`

### Factorías mínimas <a id="factor&#xED;as-m&#xED;nimas"></a>

Todos los modelos deberían tener una factoría mínima asociada. La factoría mínima es aquella que contiene el mínimo número de atributos necesarios para que el objeto sea válido. Como referencia tenemos [este post](https://thoughtbot.com/blog/factories-should-be-the-bare-minimum)

### Linting de factorías <a id="linting-de-factor&#xED;as"></a>

Para utilizar este linting podemos utilizar una tarea rake como la que se muestra en [la documentación](https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md#linting-factories)

```text
# lib/tasks/factory_bot.rake
# This rake task has been taken from: https://github.com/thoughtbot/factory_bot/blob/master/GETTING_STARTED.md
require 'English'
namespace :factory_bot do
  desc "Verify that all FactoryBot factories are valid"
  task lint: :environment do
    if Rails.env.test?
      conn = ActiveRecord::Base.connection
      conn.transaction do
        FactoryBot.lint traits: true
        raise ActiveRecord::Rollback
      end
    else
      system("bundle exec rake factory_bot:lint RAILS_ENV='test'")
      raise if $CHILD_STATUS.exitstatus.nonzero?
    end
  end
end
```

Y luego ejecutar esta rake dentro del job que ejecute los tests en nuestro CI. Por ejemplo, en gitlab:

```text
rspec:
  stage: test
  # ...
  script:
    # ...
    - bundle exec rails factory_bot:lint
    - bundle exec rspec
    # ...
```

