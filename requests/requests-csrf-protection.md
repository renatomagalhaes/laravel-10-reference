# Proteção contra CSRF

Cross-Site Request Forgery (CSRF) é um ataque que força um usuário a executar ações indesejadas em uma aplicação web na qual está autenticado. O Laravel possui proteção contra CSRF embutida, que pode ser facilmente configurada e utilizada.

## Entendendo CSRF

CSRF é um tipo de ataque que ocorre quando um usuário autenticado é induzido a enviar uma requisição não desejada para um site onde está autenticado. Isso pode resultar em ações maliciosas como mudanças de senha, transferências de fundos ou outras operações não autorizadas.

## Proteção Padrão no Laravel

### Passo 1: Middleware CSRF

O middleware CSRF do Laravel é aplicado por padrão ao grupo de middleware `web`.

```php
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
    ],
];
```

### Passo 2: Tokens CSRF em Formulários

Os tokens CSRF são automaticamente incluídos em todos os formulários Blade usando a diretiva `@csrf`.

```blade
<form action="/profile" method="POST">
    @csrf
    <!-- Campos do formulário -->
    <button type="submit">Salvar</button>
</form>
```

## Exceções de CSRF

### Passo 1: Definir Exceções no Middleware

Você pode definir rotas ou URLs específicas para serem excluídas da verificação CSRF no middleware `VerifyCsrfToken`.

```php
namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as Middleware;

class VerifyCsrfToken extends Middleware
{
    protected $except = [
        'webhook/*',
    ];
}
```

### Passo 2: Usar Tokens CSRF em Requisições AJAX

Para incluir tokens CSRF em requisições AJAX, você pode adicionar o token ao cabeçalho da requisição.

```javascript
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```

## Geração de Tokens CSRF

### Passo 1: Obter o Token CSRF

Você pode obter o token CSRF usando a função `csrf_token()`.

```php
$token = csrf_token();
```

### Passo 2: Incluir o Token CSRF Manualmente

Você pode incluir o token CSRF manualmente em requisições se necessário.

```html
<input type="hidden" name="_token" value="{{ csrf_token() }}">
```

## Configuração Avançada de CSRF

### Passo 1: Tempo de Expiração de Tokens

Você pode configurar o tempo de expiração dos tokens CSRF no arquivo de configuração `session.php`.

```php
'csrf_expire' => 120, // Em minutos
```

### Passo 2: Regenerar Tokens CSRF

Os tokens CSRF são regenerados automaticamente em cada requisição, mas você pode forçar a regeneração se necessário.

```php
session()->regenerateToken();
```

## Resumo

A proteção contra CSRF é essencial para proteger sua aplicação contra ataques maliciosos. O Laravel facilita a implementação dessa proteção através do middleware CSRF e da inclusão automática de tokens CSRF em formulários Blade. Compreender e configurar corretamente a proteção contra CSRF ajuda a garantir que sua aplicação permaneça segura contra esses tipos de ataques.
