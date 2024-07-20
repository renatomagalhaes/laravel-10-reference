# Cenário Multitenant

No cenário multitenant, cada cliente pode ter seu próprio banco de dados. A lógica para alterar a conexão com base no cliente pode ser implementada utilizando middleware ou outras abordagens.

## Exemplo de Middleware para Multitenancy

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Config;

class SetTenantDatabase
{
    public function handle($request, Closure $next)
    {
        $tenant = $request->header('X-Tenant-ID');

        if ($tenant) {
            Config::set('database.connections.mysql.database', 'tenant_' . $tenant);
            DB::purge('mysql');
            DB::reconnect('mysql');
        }

        return $next($request);
    }
}
```
