# Escopos

Escopos permitem definir consultas reutilizáveis que podem ser aplicadas aos modelos Eloquent.

## Exemplo de Escopo

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function scopeActive($query)
    {
        return $query->where('active', 1);
    }
}

// Usando o escopo
$activeUsers = User::active()->get();
```
