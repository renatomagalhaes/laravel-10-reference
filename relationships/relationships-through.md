# Relacionamento "através de"

Os relacionamentos "através de" (Has Many Through) permitem definir relações onde um modelo está relacionado a outro modelo através de um terceiro modelo.

## Definindo um Relacionamento "através de"

Para definir um relacionamento "através de", você deve usar o método `hasManyThrough` no modelo pai e definir a cadeia de relacionamentos.

### Exemplo de Relacionamento "através de"

Vamos considerar um exemplo onde um `Country` tem muitos `Post` através de `User`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Country extends Model
{
    public function posts()
    {
        return $this->hasManyThrough(Post::class, User::class);
    }
}

class User extends Model
{
    public function country()
    {
        return $this->belongsTo(Country::class);
    }

    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}

class Post extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

## Consultando um Relacionamento "através de"

Você pode acessar o relacionamento "através de" usando métodos dinâmicos Eloquent.

### Exemplo de Consulta

```php
$country = Country::find(1);
$posts = $country->posts;
```

## Filtrando e Ordenando

Você pode aplicar filtros e ordenações ao relacionamento "através de".

### Exemplo de Filtragem e Ordenação

```php
$country = Country::find(1);
$posts = $country->posts()->where('title', 'like', '%Laravel%')->orderBy('created_at', 'desc')->get();
```

## Usando o método `with`

Para otimizar a performance, você pode usar o método `with` para carregar os relacionamentos.

### Exemplo de Uso do Método `with`

```php
$countries = Country::with('posts')->get();
```

## Resumo

Os relacionamentos "através de" são úteis para definir associações indiretas entre modelos através de um terceiro modelo. O Eloquent facilita a definição, consulta e otimização dessas relações, permitindo um código limpo e eficiente.
