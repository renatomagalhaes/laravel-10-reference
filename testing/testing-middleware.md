# Testes de Middleware

Os middlewares são responsáveis por manipular as requisições HTTP antes que elas alcancem os controladores. Testar middlewares garante que as requisições sejam processadas corretamente e que as regras de negócio aplicadas nos middlewares sejam respeitadas.

## Importância dos Testes de Middleware

### Benefícios

- **Segurança:** Asseguram que as requisições sejam filtradas corretamente, evitando acessos não autorizados.
- **Integridade:** Garantem que as regras de negócio aplicadas nos middlewares sejam consistentes.
- **Confiabilidade:** Verificam que as requisições são processadas de acordo com as expectativas.

## Estrutura Básica de um Teste de Middleware

### Exemplo de Middleware

Crie um middleware que verifica se o usuário está autenticado. Se não estiver, redireciona para a página de login.

#### Arquivo do Middleware

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Auth;

class EnsureAuthenticated
{
    public function handle($request, Closure $next)
    {
        if (!Auth::check()) {
            return redirect('login');
        }

        return $next($request);
    }
}
```

### Registrando o Middleware

Registre o middleware no arquivo `app/Http/Kernel.php`:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'auth.custom' => \App\Http\Middleware\EnsureAuthenticated::class,
];
```

## Criando Testes de Middleware

### Passo 1: Criar um Teste de Middleware

Use o PHPUnit para criar um teste de middleware. Crie um arquivo de teste em `tests/Feature/MiddlewareTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Http\Middleware\EnsureAuthenticated;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class MiddlewareTest extends TestCase
{
    use RefreshDatabase;

    public function testMiddlewareRedirectsUnauthenticatedUsers()
    {
        $middleware = new EnsureAuthenticated();

        $request = Request::create('/some-protected-route', 'GET');
        $response = $middleware->handle($request, function () {});

        $this->assertEquals(302, $response->getStatusCode());
        $this->assertEquals('login', $response->headers->get('Location'));
    }

    public function testMiddlewareAllowsAuthenticatedUsers()
    {
        $this->be(\App\Models\User::factory()->create());

        $middleware = new EnsureAuthenticated();

        $request = Request::create('/some-protected-route', 'GET');
        $response = $middleware->handle($request, function () {
            return new Response('Next middleware');
        });

        $this->assertEquals('Next middleware', $response->getContent());
    }
}
```

### Passo 2: Executar o Teste

Para executar o teste, use o PHPUnit:

```bash
vendor/bin/phpunit --filter MiddlewareTest
```

## Testando Middlewares com Pest

### Exemplo de Teste com Pest

Crie um teste de middleware com Pest:

```php
use App\Http\Middleware\EnsureAuthenticated;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

it('redirects unauthenticated users', function () {
    $middleware = new EnsureAuthenticated();

    $request = Request::create('/some-protected-route', 'GET');
    $response = $middleware->handle($request, function () {});

    expect($response->getStatusCode())->toBe(302);
    expect($response->headers->get('Location'))->toBe('login');
});

it('allows authenticated users', function () {
    $this->be(\App\Models\User::factory()->create());

    $middleware = new EnsureAuthenticated();

    $request = Request::create('/some-protected-route', 'GET');
    $response = $middleware->handle($request, function () {
        return new Response('Next middleware');
    });

    expect($response->getContent())->toBe('Next middleware');
});
```

## Práticas Recomendadas

### Testar Cenários Positivos e Negativos

Teste tanto os cenários em que o middleware deve bloquear a requisição quanto os em que deve permitir.

### Usar Factories para Dados de Teste

Use factories para criar usuários autenticados e outros dados necessários para os testes.

### Isolar a Lógica do Middleware

Garanta que a lógica do middleware seja isolada e fácil de testar, evitando dependências complexas.

## Exemplo Completo

### Middleware EnsureAuthenticated

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Support\Facades\Auth;

class EnsureAuthenticated
{
    public function handle($request, Closure $next)
    {
        if (!Auth::check()) {
            return redirect('login');
        }

        return $next($request);
    }
}
```

### Teste de Middleware

No arquivo `tests/Feature/MiddlewareTest.php`, escreva o teste de middleware:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;
use App\Http\Middleware\EnsureAuthenticated;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class MiddlewareTest extends TestCase
{
    use RefreshDatabase;

    public function testMiddlewareRedirectsUnauthenticatedUsers()
    {
        $middleware = new EnsureAuthenticated();

        $request = Request::create('/some-protected-route', 'GET');
        $response = $middleware->handle($request, function () {});

        $this->assertEquals(302, $response->getStatusCode());
        $this->assertEquals('login', $response->headers->get('Location'));
    }

    public function testMiddlewareAllowsAuthenticatedUsers()
    {
        $this->be(\App\Models\User::factory()->create());

        $middleware = new EnsureAuthenticated();

        $request = Request::create('/some-protected-route', 'GET');
        $response = $middleware->handle($request, function () {
            return new Response('Next middleware');
        });

        $this->assertEquals('Next middleware', $response->getContent());
    }
}
```

## Resumo

Testar middlewares é crucial para garantir que as requisições HTTP sejam processadas corretamente e que as regras de negócio aplicadas nos middlewares sejam respeitadas. Com PHPUnit e Pest, você pode escrever testes de middleware eficientes que garantem a segurança, integridade e confiabilidade da sua aplicação Laravel.
