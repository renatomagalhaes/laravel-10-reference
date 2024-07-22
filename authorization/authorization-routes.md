# Autorização em Rotas

A autorização em rotas no Laravel permite restringir o acesso a certas rotas com base nas permissões dos usuários. Este guia aborda como implementar autorização em rotas para proteger partes específicas da sua aplicação.

## Introdução à Autorização em Rotas

### O que é Autorização em Rotas?

A autorização em rotas é o processo de verificar as permissões dos usuários antes de permitir o acesso a uma rota específica. Isso é feito para garantir que apenas usuários autorizados possam acessar determinados recursos ou executar certas ações.

### Benefícios da Autorização em Rotas

- **Segurança:** Protege recursos sensíveis e funcionalidades críticas da aplicação.
- **Controle:** Permite um controle granular sobre o acesso a diferentes partes da aplicação.
- **Flexibilidade:** Facilita a implementação de diferentes níveis de acesso com base nas permissões dos usuários.

## Implementando Autorização em Rotas

### Usando Middleware de Autorização

A maneira mais comum de implementar autorização em rotas é usando middleware. Middleware de autorização verifica as permissões dos usuários antes de permitir o acesso a uma rota.

#### Exemplo Básico de Middleware

Crie um middleware que verifica se o usuário tem uma função específica:

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

Registre o middleware no arquivo `Kernel.php`:

```php
protected $routeMiddleware = [
    'role' => \App\Http\Middleware\CheckRole::class,
];
```

Use o middleware nas rotas:

```php
Route::get('/admin', [AdminController::class, 'index'])->middleware('role:admin');
```

### Usando Gates em Rotas

Gates fornecem uma maneira flexível de definir regras de autorização que podem ser aplicadas diretamente nas rotas.

#### Definindo um Gate

Defina um gate no `AuthServiceProvider`:

```php
Gate::define('view-dashboard', function ($user) {
    return $user->isAdmin();
});
```

#### Usando um Gate em uma Rota

Verifique a autorização usando o gate diretamente na rota:

```php
Route::get('/dashboard', function () {
    if (Gate::denies('view-dashboard')) {
        abort(403);
    }

    return view('dashboard');
});
```

## Exemplos Práticos

### Protegendo Rotas de Administrador

#### Criando Middleware para Verificar Função de Administrador

Crie um middleware que verifica se o usuário tem a função de administrador:

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
    'admin' => \App\Http\Middleware\AdminMiddleware::class,
];
```

```php
Route::get('/admin', [AdminController::class, 'index'])->middleware('admin');
```

### Usando Gates para Verificação de Permissões

#### Definindo um Gate

Defina um gate para verificar se o usuário pode acessar o painel de controle:

```php
Gate::define('view-dashboard', function ($user) {
    return $user->hasRole('admin');
});
```

#### Usando o Gate na Rota

Verifique a autorização usando o gate diretamente na rota:

```php
Route::get('/dashboard', function () {
    if (Gate::denies('view-dashboard')) {
        abort(403);
    }

    return view('dashboard');
});
```

## Resumo

A autorização em rotas no Laravel é uma prática essencial para proteger partes específicas da sua aplicação. Usando middleware e gates, você pode implementar verificação de permissões de maneira flexível e eficaz, garantindo que apenas usuários autorizados possam acessar recursos sensíveis e executar ações críticas. Com a autorização em rotas, você pode manter a segurança e a integridade da sua aplicação Laravel.
