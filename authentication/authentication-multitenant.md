# Exemplo de Multitenant

A arquitetura multitenant permite que uma única aplicação sirva a múltiplos clientes (tenants), cada um com seus próprios dados e configurações. No Laravel, você pode configurar um sistema multitenant utilizando diversas abordagens, como bancos de dados separados ou colunas de tenant_id nas tabelas.

## Configurando um Sistema Multitenant com Tenant_id

### Passo 1: Estrutura do Banco de Dados

Adicione uma coluna `tenant_id` às suas tabelas que precisam ser multitenant. Por exemplo:

```bash
php artisan make:migration add_tenant_id_to_users_table --table=users
```

No arquivo de migração, adicione a coluna `tenant_id`:

```php
public function up()
{
    Schema::table('users', function (Blueprint $table) {
        $table->unsignedBigInteger('tenant_id')->nullable()->index();
    });
}
```

### Passo 2: Middleware de Tenant

Crie um middleware para identificar o tenant com base no subdomínio ou outro identificador.

```bash
php artisan make:middleware IdentifyTenant
```

### Exemplo de Middleware

```php
namespace App\Http\Middleware;

use Closure;
use App\Models\Tenant;

class IdentifyTenant
{
    public function handle($request, Closure $next)
    {
        $host = $request->getHost();
        $subdomain = explode('.', $host)[0];

        $tenant = Tenant::where('subdomain', $subdomain)->first();

        if (!$tenant) {
            abort(404, 'Tenant not found');
        }

        session(['tenant_id' => $tenant->id]);

        return $next($request);
    }
}
```

### Passo 3: Aplicar o Middleware

Aplique o middleware às suas rotas no arquivo `app/Http/Kernel.php`:

```php
protected $middlewareGroups = [
    'web' => [
        // outros middlewares
        \App\Http\Middleware\IdentifyTenant::class,
    ],
];
```

### Passo 4: Filtrar por Tenant_id nos Modelos

Modifique seus modelos para filtrar por `tenant_id`:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Builder;
use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    protected static function booted()
    {
        static::addGlobalScope('tenant', function (Builder $builder) {
            $builder->where('tenant_id', session('tenant_id'));
        });
    }
}
```

### Passo 5: Atribuir Tenant_id ao Criar Registros

Ao criar novos registros, certifique-se de atribuir o `tenant_id`:

```php
namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $data = $request->all();
        $data['tenant_id'] = session('tenant_id');
        User::create($data);
        
        return redirect()->route('users.index');
    }
}
```

## Resumo

A configuração de um sistema multitenant no Laravel usando a coluna `tenant_id` envolve a modificação da estrutura do banco de dados, a criação de um middleware para identificar o tenant, a aplicação de filtros nos modelos e a garantia de que todos os novos registros incluam o `tenant_id`. Essa abordagem permite que uma única aplicação sirva múltiplos clientes com seus próprios dados isolados.
