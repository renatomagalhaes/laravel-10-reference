# Middleware de Autorização

O middleware de autorização no Laravel permite verificar permissões de acesso antes de permitir que um usuário acesse certas rotas ou execute ações específicas. Este guia aborda como criar e usar middlewares de autorização para proteger sua aplicação.

## Introdução ao Middleware de Autorização

### O que é Middleware de Autorização?

Middleware de autorização é uma camada intermediária que intercepta solicitações HTTP antes que elas alcancem o controlador. Ele verifica se o usuário tem as permissões necessárias para acessar um recurso ou executar uma ação e pode bloquear o acesso se as permissões não forem atendidas.

### Benefícios do Middleware de Autorização

- **Segurança:** Garante que apenas usuários autorizados possam acessar recursos protegidos.
- **Centralização:** Permite centralizar a lógica de autorização, facilitando a manutenção.
- **Flexibilidade:** Pode ser aplicado a rotas individuais ou grupos de rotas.

## Criando Middleware de Autorização

### Passo 1: Gerar o Middleware

Use o comando Artisan `make:middleware` para gerar um novo middleware:

```bash
php artisan make:middleware CheckRole
```

### Passo 2: Definir a Lógica de Autorização

Abra o arquivo gerado em `app/Http/Middleware/CheckRole.php` e defina a lógica de autorização:

```php
namespace App\Http\Middleware;

use Closure;

class CheckRole
{
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            abort(403, 'Ação não autorizada.');
        }

        return $next($request);
    }
}
```

### Passo 3: Registrar o Middleware

Registre o middleware no arquivo `app/Http/Kernel.php`:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'role' => \App\Http\Middleware\CheckRole::class,
];
```

## Usando Middleware de Autorização

### Aplicando Middleware a Rotas

Use o middleware nas rotas para verificar as permissões dos usuários:

```php
use App\Http\Controllers\AdminController;

Route::get('/admin', [AdminController::class, 'index'])->middleware('role:admin');
```

### Aplicando Middleware a Grupos de Rotas

Aplique o middleware a grupos de rotas para proteger várias rotas com a mesma lógica de autorização:

```php
Route::middleware(['role:admin'])->group(function () {
    Route::get('/admin', [AdminController::class, 'index']);
    Route::get('/admin/settings', [AdminController::class, 'settings']);
});
```

## Middleware Personalizado

### Middleware com Várias Funções

Crie middleware que aceite várias funções, permitindo maior flexibilidade na verificação de permissões:

```php
namespace App\Http\Middleware;

use Closure;

class CheckRoles
{
    public function handle($request, Closure $next, ...$roles)
    {
        foreach ($roles as $role) {
            if ($request->user()->hasRole($role)) {
                return $next($request);
            }
        }

        abort(403, 'Ação não autorizada.');
    }
}
```

### Registrando Middleware com Várias Funções

Registre o middleware no arquivo `Kernel.php`:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'roles' => \App\Http\Middleware\CheckRoles::class,
];
```

### Aplicando Middleware com Várias Funções

Use o middleware com várias funções nas rotas:

```php
Route::get('/dashboard', [DashboardController::class, 'index'])->middleware('roles:admin,manager');
```

## Exemplos Práticos

### Protegendo Rotas de Administrador

#### Criando Middleware para Verificar Função de Administrador

```php
namespace App\Http\Middleware;

use Closure;

class AdminMiddleware
{
    public function handle($request, Closure $next)
    {
        if (! $request->user()->hasRole('admin')) {
            abort(403, 'Ação não autorizada.');
        }

        return $next($request);
    }
}
```

#### Registrando e Usando o Middleware

Registre o middleware no `Kernel.php` e aplique-o às rotas de administrador:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'admin' => \App\Http\Middleware\AdminMiddleware::class,
];
```

```php
Route::get('/admin', [AdminController::class, 'index'])->middleware('admin');
```

## Resumo

O middleware de autorização no Laravel é uma ferramenta poderosa para proteger rotas e recursos sensíveis na aplicação. Usando middlewares, você pode centralizar a lógica de autorização, aplicar verificações de permissões a rotas individuais ou grupos de rotas e criar middlewares personalizados para atender a necessidades específicas, garantindo a segurança e a integridade da sua aplicação.
