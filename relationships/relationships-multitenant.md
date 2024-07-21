# Cenário Multitenant

Em um cenário multitenant, múltiplos clientes (tenants) compartilham a mesma aplicação, mas cada cliente possui seus próprios dados. Implementar relacionamentos em um ambiente multitenant requer cuidado para garantir que os dados de cada cliente sejam isolados corretamente.

## Definindo um Cenário Multitenant

Para definir um cenário multitenant, você deve garantir que cada consulta esteja filtrada pelo tenant atual. Isso pode ser feito utilizando um middleware ou escopos globais.

### Exemplo de Middleware Multitenant

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Auth;

class SetTenant
{
    public function handle($request, Closure $next)
    {
        if (Auth::check()) {
            $tenantId = Auth::user()->tenant_id;
            \DB::statement("SET @tenant_id = {$tenantId}");
        }

        return $next($request);
    }
}
```

### Exemplo de Escopo Global Multitenant

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class TenantModel extends Model
{
    protected static function booted()
    {
        static::addGlobalScope('tenant', function (Builder $builder) {
            $builder->where('tenant_id', auth()->user()->tenant_id);
        });
    }
}

class User extends TenantModel
{
    // Relacionamentos multitenant são definidos normalmente
    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

### Consultando Relacionamentos Multitenant

```php
$user = User::with('posts')->find(1);
$posts = $user->posts;
```

## Resumo

Em um cenário multitenant, garantir o isolamento correto dos dados de cada cliente é crucial. Utilizar middleware ou escopos globais pode ajudar a garantir que cada consulta esteja adequadamente filtrada pelo tenant atual.
