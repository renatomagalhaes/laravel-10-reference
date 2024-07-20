# Accessors e Mutators

Accessors e Mutators permitem manipular valores de atributos antes de acessá-los ou salvá-los. Eles são métodos no modelo Eloquent que transformam os valores dos atributos.

## Accessors

Os Accessors permitem que você formate um valor de atributo ao acessá-lo.

### Exemplo de Accessor

\```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // Definindo um Accessor para o atributo full_name
    public function getFullNameAttribute()
    {
        return "{$this->first_name} {$this->last_name}";
    }
}

// Usando o Accessor
$user = User::find(1);
echo $user->full_name; // Output: John Doe
\```

## Mutators

Os Mutators permitem que você altere o valor de um atributo antes de salvá-lo no banco de dados.

### Exemplo de Mutator

\```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // Definindo um Mutator para o atributo password
    public function setPasswordAttribute($value)
    {
        $this->attributes['password'] = bcrypt($value);
    }
}

// Usando o Mutator
$user = new User;
$user->password = 'secret';
$user->save();
\```

## Combinação de Accessors e Mutators

Você pode combinar Accessors e Mutators para transformar os valores de atributos ao acessá-los e salvá-los.

### Exemplo de Accessor e Mutator Combinados

\```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    // Accessor para o atributo name
    public function getNameAttribute($value)
    {
        return ucfirst($value);
    }

    // Mutator para o atributo name
    public function setNameAttribute($value)
    {
        $this->attributes['name'] = strtolower($value);
    }
}

// Usando o Accessor e Mutator
$user = new User;
$user->name = 'JOHN DOE';
$user->save();

$user = User::find(1);
echo $user->name; // Output: John doe
\```

## Resumo

Accessors e Mutators são ferramentas poderosas no Eloquent para transformar valores de atributos de maneira consistente. Eles permitem definir como os valores são armazenados no banco de dados e como são recuperados.

\```
