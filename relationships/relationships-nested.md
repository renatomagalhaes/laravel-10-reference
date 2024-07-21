# Relacionamentos Aninhados

Relacionamentos aninhados permitem que você trabalhe com múltiplos níveis de relações de uma forma eficiente e organizada. Isso é útil para cenários complexos onde os modelos possuem várias camadas de relacionamentos.

## Definindo Relacionamentos Aninhados

Você pode definir relacionamentos aninhados em Eloquent usando métodos encadeados para acessar relações de múltiplos níveis.

### Exemplo de Relacionamento Aninhado

Vamos considerar um exemplo onde `Country` tem muitos `State`, que por sua vez tem muitos `City`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Country extends Model
{
    public function states()
    {
        return $this->hasMany(State::class);
    }
}

class State extends Model
{
    public function country()
    {
        return $this->belongsTo(Country::class);
    }

    public function cities()
    {
        return $this->hasMany(City::class);
    }
}

class City extends Model
{
    public function state()
    {
        return $this->belongsTo(State::class);
    }
}
```

### Consultando Relacionamentos Aninhados

```php
$country = Country::with('states.cities')->find(1);
$states = $country->states;
foreach ($states as $state) {
    $cities = $state->cities;
}
```

## Resumo

Relacionamentos aninhados permitem que você trabalhe com múltiplos níveis de relações de forma eficiente. Utilizar a carga adiantada (eager loading) ajuda a otimizar essas consultas, reduzindo o número de queries ao banco de dados.
