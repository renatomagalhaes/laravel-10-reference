# Global Middleware

O middleware no Laravel permite que você filtre as requisições HTTP que entram na sua aplicação. Middleware global é aplicado a todas as requisições e pode ser usado para uma variedade de propósitos, como autenticação, logging, manipulação de cabeçalhos e muito mais.

## Criando Middleware

### Passo 1: Criar Middleware

Você pode criar um middleware usando o comando Artisan `make:middleware`.

```bash
php artisan make:middleware LogRequest
```

### Passo 2: Implementar a Lógica do Middleware

Dentro da classe do middleware, implemente a lógica desejada.

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class LogRequest
{
    public function handle(Request $request, Closure $next)
    {
        Log::info('Request Logged:', ['url' => $request->url(), 'method' => $request->method()]);

        return $next($request);
    }
}
```

## Registrando Middleware Global

### Passo 1: Registrar Middleware no Kernel

Para registrar o middleware como global, adicione-o ao array `$middleware` no arquivo `app/Http/Kernel.php`.

```php
protected $middleware = [
    // Outros middlewares
    \App\Http\Middleware\LogRequest::class,
];
```

## Aplicações Comuns de Middleware Global

### 1. Autenticação

Você pode usar middleware global para garantir que todas as requisições sejam autenticadas.

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class Authenticate
{
    public function handle(Request $request, Closure $next)
    {
        if (!Auth::check()) {
            return redirect('login');
        }

        return $next($request);
    }
}
```

### 2. Manipulação de Cabeçalhos

Middleware global pode ser usado para adicionar ou modificar cabeçalhos em todas as respostas.

```php
namespace App\Http\Middleware;

use Closure;

class AddHeaders
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);
        $response->headers->set('X-Custom-Header', 'HeaderValue');
        
        return $response;
    }
}
```

### 3. Logging de Requisições

Você pode registrar todas as requisições que entram na aplicação para fins de auditoria e debugging.

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class LogRequest
{
    public function handle(Request $request, Closure $next)
    {
        Log::info('Request Logged:', ['url' => $request->url(), 'method' => $request->method(), 'ip' => $request->ip()]);

        return $next($request);
    }
}
```

### 4. Definir Idioma Padrão

Middleware global pode ser usado para definir o idioma padrão com base no cabeçalho `Accept-Language` da requisição.

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\App;

class SetLocale
{
    public function handle($request, Closure $next)
    {
        $locale = $request->header('Accept-Language', 'en');
        App::setLocale($locale);

        return $next($request);
    }
}
```

## Resumo

Middleware global no Laravel permite aplicar filtros e manipulações a todas as requisições que entram na aplicação. Isso é útil para tarefas como autenticação, logging, manipulação de cabeçalhos e configuração de idioma. Criar e registrar middleware global é simples e fornece uma maneira poderosa de gerenciar o fluxo de requisições na sua aplicação.
