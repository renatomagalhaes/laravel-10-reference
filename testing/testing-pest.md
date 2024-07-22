# Testes com Pest

Pest é um framework de testes PHP moderno e elegante que oferece uma sintaxe simples e intuitiva para escrever testes. Ele é construído sobre o PHPUnit, mas oferece uma API mais expressiva e legível.

## Importância dos Testes com Pest

### Benefícios

- **Sintaxe Simples:** Oferece uma sintaxe clara e concisa, facilitando a escrita e a leitura dos testes.
- **Produtividade:** Aumenta a produtividade dos desenvolvedores ao simplificar o processo de criação de testes.
- **Compatibilidade:** Totalmente compatível com PHPUnit, permitindo a integração com testes existentes.

## Configuração Básica do Pest

### Instalação do Pest

Instale o Pest via Composer:

```bash
composer require pestphp/pest --dev
composer require pestphp/pest-plugin-laravel --dev
```

### Inicialização do Pest

Inicialize o Pest no seu projeto:

```bash
./vendor/bin/pest --init
```

## Escrevendo Testes com Pest

### Teste Simples

Crie um teste simples com Pest:

```php
it('asserts true is true', function () {
    expect(true)->toBeTrue();
});
```

### Teste de Requisição HTTP

Escreva um teste que faça uma requisição HTTP usando Pest:

```php
use App\Models\User;

it('authenticates a user', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'password',
    ]);

    $response->assertRedirect('/home');
    expect(auth()->user()->email)->toBe('test@example.com');
});
```

### Teste de Banco de Dados

Escreva um teste que interaja com o banco de dados:

```php
use App\Models\User;

it('creates a user in the database', function () {
    User::factory()->create([
        'email' => 'test@example.com',
    ]);

    expect('users')->toHaveRecord(['email' => 'test@example.com']);
});
```

## Testes de Middleware com Pest

### Exemplo de Teste de Middleware

Crie um teste de middleware usando Pest:

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

### Manter Testes Simples e Claros

Aproveite a sintaxe simples do Pest para escrever testes que sejam fáceis de ler e entender.

### Usar Factories para Criar Dados de Teste

Utilize factories para criar usuários e outros dados de teste de forma consistente.

### Integrar com Ferramentas de CI

Configure o Pest para ser executado automaticamente em seu pipeline de CI, garantindo que os testes sejam executados sempre que houver alterações no código.

## Exemplo Completo

### Teste de Autenticação com Pest

Crie um arquivo de teste em `tests/Feature/AuthenticationTest.php`:

```php
use App\Models\User;

it('authenticates a user', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'password',
    ]);

    $response->assertRedirect('/home');
    expect(auth()->user()->email)->toBe('test@example.com');
});

it('fails to authenticate with invalid credentials', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'invalid-password',
    ]);

    $response->assertSessionHasErrors('email');
    expect(auth()->check())->toBeFalse();
});
```

### Teste de Criação de Usuário com Pest

Crie um arquivo de teste em `tests/Feature/UserTest.php`:

```php
use App\Models\User;

it('creates a user in the database', function () {
    User::factory()->create([
        'email' => 'test@example.com',
    ]);

    expect('users')->toHaveRecord(['email' => 'test@example.com']);
});
```

## Resumo

Pest é um framework de testes PHP moderno que oferece uma sintaxe simples e intuitiva, tornando a escrita e a leitura de testes mais fáceis e agradáveis. Com Pest, você pode escrever testes eficientes e manter a qualidade do seu código, garantindo a confiabilidade e a estabilidade da sua aplicação Laravel.
