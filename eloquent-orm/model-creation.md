# Criação de Modelos

Modelos são a forma principal de interação com o banco de dados usando Eloquent. Um modelo em Eloquent é uma classe PHP que estende a classe `Illuminate\Database\Eloquent\Model`.

## Exemplo de Modelo

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    protected $fillable = ['name', 'email', 'password'];
}
```

## Definindo Tabelas e Chaves Primárias

Por padrão, o Eloquent assume que a tabela correspondente a um modelo tem o nome plural do modelo. Você pode personalizar isso.

```php
class User extends Model
{
    protected $table = 'my_users';
    protected $primaryKey = 'user_id';
}
```
