# Autorização Multi-Tenancy

Implementar autorização em um ambiente multi-tenancy no Laravel exige uma abordagem específica para garantir que cada tenant (inquilino) possa acessar apenas seus próprios recursos. Este guia aborda como configurar e gerenciar a autorização em uma aplicação multi-tenant.

## Introdução à Autorização Multi-Tenancy

### O que é Multi-Tenancy?

Multi-tenancy é uma arquitetura de software onde uma única instância da aplicação serve múltiplos tenants, cada um com seus próprios dados e recursos isolados. Cada tenant opera como uma entidade separada dentro da mesma aplicação.

### Benefícios da Autorização Multi-Tenancy

- **Isolamento de Dados:** Garante que os dados de cada tenant são isolados e acessíveis apenas por usuários autorizados.
- **Segurança:** Protege os recursos de cada tenant contra acessos não autorizados.
- **Escalabilidade:** Facilita o crescimento e a gestão de múltiplos tenants dentro da mesma aplicação.

## Configurando Multi-Tenancy no Laravel

### Passo 1: Estrutura de Banco de Dados

Configure o banco de dados para suportar multi-tenancy. Uma abordagem comum é adicionar uma coluna `tenant_id` em todas as tabelas que precisam ser isoladas por tenant:

```php
Schema::table('posts', function (Blueprint $table) {
    $table->unsignedBigInteger('tenant_id');

    $table->foreign('tenant_id')->references('id')->on('tenants')->onDelete('cascade');
});
```

### Passo 2: Modelo Tenant

Crie um modelo para gerenciar os tenants:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Tenant extends Model
{
    use HasFactory;

    public function users()
    {
        return $this->hasMany(User::class);
    }

    public function posts()
    {
        return $this->hasMany(Post::class);
    }
}
```

### Passo 3: Middleware Multi-Tenancy

Crie um middleware para definir o tenant atual com base no usuário autenticado:

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Auth;

class SetTenant
{
    public function handle($request, Closure $next)
    {
        if (Auth::check()) {
            $tenant = Auth::user()->tenant;

            if ($tenant) {
                // Defina o tenant no contexto da aplicação
                app()->instance('tenant', $tenant);
            }
        }

        return $next($request);
    }
}
```

Registre o middleware no `Kernel.php`:

```php
protected $middlewareGroups = [
    'web' => [
        // Outros middlewares
        \App\Http\Middleware\SetTenant::class,
    ],
];
```

## Usando Gates e Policies em Multi-Tenancy

### Definindo Gates e Policies com Tenant

Adapte suas policies e gates para verificar o `tenant_id`:

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function view(User $user, Post $post)
    {
        return $user->tenant_id === $post->tenant_id;
    }

    public function update(User $user, Post $post)
    {
        return $user->tenant_id === $post->tenant_id && $user->id === $post->user_id;
    }
}
```

Registre a policy no `AuthServiceProvider`:

```php
protected $policies = [
    Post::class => PostPolicy::class,
];
```

### Verificando Tenant em Controladores

Utilize o tenant atual no controlador para filtrar recursos:

```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function index()
    {
        $tenant = app('tenant');

        $posts = Post::where('tenant_id', $tenant->id)->get();

        return view('posts.index', compact('posts'));
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // Lógica de atualização do post
    }
}
```

### Middleware para Verificar Tenant

Adicione um middleware para garantir que todas as solicitações estão verificando o tenant:

```php
namespace App\Http\Middleware;

use Closure;

class EnsureTenant
{
    public function handle($request, Closure $next)
    {
        $tenant = app('tenant');

        if (! $tenant) {
            abort(403, 'Acesso não autorizado.');
        }

        return $next($request);
    }
}
```

Registre o middleware no `Kernel.php`:

```php
protected $routeMiddleware = [
    'tenant' => \App\Http\Middleware\EnsureTenant::class,
];
```

Use o middleware nas rotas:

```php
Route::middleware(['tenant'])->group(function () {
    Route::get('/posts', [PostController::class, 'index']);
    Route::post('/posts/{post}', [PostController::class, 'update']);
});
```

## Resumo

Implementar autorização em um ambiente multi-tenancy no Laravel envolve configurar o banco de dados, modelos, middleware e políticas para garantir que cada tenant possa acessar apenas seus próprios recursos. Usando essas técnicas, você pode construir uma aplicação segura, escalável e organizada para múltiplos tenants.
