# Gemfile

En el Gemfile de cada proyecto, por defecto, nos aseguramos de:

* Versionar cada gema que usemos a nivel de patch con el especificador `~>`
* La versión de rails siempre irá fijada
* Si la gema no se necesita para la ejecución de la aplicación nos aseguraremos de no requerirla por defecto
* Nos aseguraremos de que cualquier gema que no necesite ejecutarse en producción pertenece a un grupo concreto
* Minimizaremos en todo lo posible las dependencias con git

```text
# Gemfile

gem 'rails', '5.2.0'

# We should try to avoid this
gem 'lazy_high_charts', git: 'https://github.com/michelson/lazy_high_charts.git'

group :development do
  gem  'rubocop', '~> 0.71.0', require: false
end

group :development, :test do
  gem 'factory_bot', '5.0.2'
end
```

Si tu proyecto no tiene el versionado pesimista, puedes utilizar la gema [`pessimize`](https://github.com/joonty/pessimize). Basta con instalarla y ejecutar:

```text
pessimize -C patch
```

