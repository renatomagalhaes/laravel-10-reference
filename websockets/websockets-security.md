# Segurança com Websockets

Garantir a segurança das comunicações via Websockets é crucial para proteger dados sensíveis e evitar acessos não autorizados. Este guia aborda práticas recomendadas e técnicas para implementar segurança em Websockets no Laravel.

## Importância da Segurança em Websockets

### Por que a Segurança é Importante?

- **Proteção de Dados:** Evita o acesso não autorizado a dados sensíveis.
- **Integridade da Aplicação:** Protege contra ataques que podem comprometer a funcionalidade.
- **Conformidade:** Garante que a aplicação atenda a requisitos regulatórios de segurança.

### Principais Ameaças

- **Interceptação de Dados:** Ataques man-in-the-middle (MITM) que interceptam dados em trânsito.
- **Acesso Não Autorizado:** Usuários não autorizados acessando canais privados.
- **Injeção de Comandos:** Injeção de comandos maliciosos através de Websockets.

## Práticas Recomendadas de Segurança

### 1. Usar HTTPS/TLS

Certifique-se de que todas as comunicações WebSocket são realizadas sobre HTTPS/TLS para criptografar dados em trânsito.

```php
'options' => [
    'encrypted' => true,
    'host' => env('PUSHER_HOST'),
    'port' => env('PUSHER_PORT'),
    'scheme' => 'https',
    'useTLS' => true,
],
```

### 2. Autenticação de Canal

Implemente a autenticação de canal para garantir que apenas usuários autorizados possam acessar canais privados e de presença.

#### Configuração no Backend

Defina a autenticação de canal em `routes/channels.php`:

```php
Broadcast::channel('private-channel.{id}', function ($user, $id) {
    return (int) $user->id === (int) $id;
});

Broadcast::channel('presence-channel', function ($user) {
    return ['id' => $user->id, 'name' => $user->name];
});
```

### 3. Verificação CSRF

Habilite a verificação CSRF para todas as solicitações WebSocket para evitar ataques cross-site request forgery (CSRF).

```js
window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER,
    forceTLS: true,
    auth: {
        headers: {
            'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').getAttribute('content')
        }
    }
});
```

### 4. Validação e Sanitização de Dados

Sempre valide e sanitize os dados recebidos e enviados via Websockets para evitar injeção de comandos e outros ataques.

```php
public function handle(WebSocketMessage $message)
{
    $this->validate($message, [
        'data' => 'required|string',
    ]);

    $data = htmlspecialchars($message->data, ENT_QUOTES, 'UTF-8');

    // Processar mensagem segura
}
```

### 5. Limitação de Taxa (Rate Limiting)

Implemente a limitação de taxa para evitar abusos e ataques de negação de serviço (DoS).

```php
use Illuminate\Cache\RateLimiter;

public function handle(WebSocketMessage $message)
{
    $limiter = app(RateLimiter::class);
    $key = 'websocket-message:' . $message->user()->id;

    if ($limiter->tooManyAttempts($key, 5)) {
        return response('Muitas tentativas.', 429);
    }

    $limiter->hit($key, 60);

    // Processar mensagem
}
```

## Exemplo Prático

### Configuração Completa de Segurança

#### Backend

Configure autenticação de canal e verificação de dados:

```php
Broadcast::channel('private-channel.{id}', function ($user, $id) {
    return (int) $user->id === (int) $id;
});

Broadcast::channel('presence-channel', function ($user) {
    return ['id' => $user->id, 'name' => $user->name];
});

use Illuminate\Cache\RateLimiter;

public function handle(WebSocketMessage $message)
{
    $this->validate($message, [
        'data' => 'required|string',
    ]);

    $data = htmlspecialchars($message->data, ENT_QUOTES, 'UTF-8');

    $limiter = app(RateLimiter::class);
    $key = 'websocket-message:' . $message->user()->id;

    if ($limiter->tooManyAttempts($key, 5)) {
        return response('Muitas tentativas.', 429);
    }

    $limiter->hit($key, 60);

    // Processar mensagem segura
}
```

#### Frontend

Configure Laravel Echo com verificação CSRF:

```js
import Echo from 'laravel-echo';
window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER,
    forceTLS: true,
    auth: {
        headers: {
            'X-CSRF-TOKEN': document.querySelector('meta[name="csrf-token"]').getAttribute('content')
        }
    }
});
```

## Resumo

Garantir a segurança das comunicações via Websockets no Laravel é essencial para proteger dados sensíveis e evitar acessos não autorizados. Implementando práticas recomendadas como uso de HTTPS/TLS, autenticação de canal, verificação CSRF, validação e sanitização de dados, e limitação de taxa, você pode proteger efetivamente sua aplicação contra ameaças comuns.
