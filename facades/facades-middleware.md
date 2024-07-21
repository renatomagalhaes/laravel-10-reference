# Facades e Middleware

Facades podem ser usadas em middleware para implementar lógica de aplicação transversal de maneira eficiente e centralizada.

## Utilizando Facades em Middleware

Você pode usar facades dentro de middleware para aplicar regras e lógica que afetam todas as requisições.

### Exemplo de Middleware

#### Middleware

```php
namespace App\Http\Middleware;

use Closure;
use App\Facades\CustomService;

class CustomMiddleware
{
    public function handle($request, Closure $next)
    {
        CustomService::performTask();

        return $next($request);
    }
}
```

#### Registrando o Middleware

Adicione o middleware ao grupo de middleware em `app/Http/Kernel.php`.

```php
protected $middleware = [
    // Other middleware

    \App\Http\Middleware\CustomMiddleware::class,
];
```

## Resumo

Utilizar facades em middleware permite centralizar a lógica transversal da aplicação, tornando o código mais organizado e facilitando a manutenção e atualização das regras de negócio.
