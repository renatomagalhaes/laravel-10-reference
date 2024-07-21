# Cenário Multitenant

Facades no Laravel podem ser usadas para gerenciar cenários multitenant, onde múltiplos clientes (tenants) compartilham a mesma aplicação, mas cada cliente possui seus próprios dados.

## Implementando Multitenancy

Para implementar multitenancy, você deve garantir que todas as consultas estejam filtradas pelo tenant atual. Isso pode ser feito utilizando middleware ou escopos globais.

### Middleware Multitenant

O middleware pode ser usado para definir o tenant atual com base no usuário autenticado.

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

### Escopos Globais Multitenant

Os escopos globais podem ser usados para garantir que todas as consultas estejam filtradas pelo tenant atual.

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

### Consultas Multitenant

```php
$user = User::with('posts')->find(1);
$posts = $user->posts;
```

## Resumo

Facades no Laravel podem ser usadas eficazmente para implementar multitenancy, garantindo o isolamento adequado dos dados de cada cliente. Utilizar middleware ou escopos globais pode ajudar a garantir que cada consulta esteja filtrada pelo tenant atual.
