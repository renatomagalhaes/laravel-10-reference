# Parametrização Backend e Frontend

Configurar Websockets requer ajustes tanto no backend quanto no frontend para garantir uma comunicação eficiente e segura. Este guia aborda como parametrizar a configuração de Websockets no Laravel, abrangendo desde a configuração do servidor até a integração com o frontend.

## Configuração do Backend

### Configuração do Servidor WebSocket

No backend, configure o Laravel Echo Server ou Pusher para gerenciar as conexões WebSocket. Aqui está um exemplo de configuração para o Laravel Echo Server.

#### Passo 1: Configuração Inicial

Instale e configure o Laravel Echo Server conforme mostrado nos passos anteriores. Certifique-se de que o arquivo `laravel-echo-server.json` esteja corretamente configurado.

#### Passo 2: Definir Eventos de Broadcast

Defina eventos no Laravel que serão broadcasted para os clientes conectados.

```php
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Queue\SerializesModels;

class MessageSent implements ShouldBroadcast
{
    use InteractsWithSockets, SerializesModels;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function broadcastOn()
    {
        return new Channel('chat');
    }
}
```

#### Passo 3: Configurar Autenticação de Canal

Para canais privados e de presença, configure a autenticação no arquivo `routes/channels.php`:

```php
Broadcast::channel('chat', function ($user) {
    return Auth::check();
});
```

### Configuração de Redis

Configure o Redis no arquivo `.env` para gerenciar as filas de eventos broadcasted:

```env
BROADCAST_DRIVER=redis
CACHE_DRIVER=redis
QUEUE_CONNECTION=redis
SESSION_DRIVER=redis
REDIS_HOST=127.0.0.1
REDIS_PASSWORD=null
REDIS_PORT=6379
```

### Iniciar o Servidor WebSocket

Inicie o Laravel Echo Server:

```bash
laravel-echo-server start
```

## Configuração do Frontend

### Integrando Laravel Echo

No frontend, use Laravel Echo para escutar eventos broadcasted. Configure Laravel Echo no arquivo JavaScript principal, como `resources/js/bootstrap.js`.

#### Passo 1: Instalar Laravel Echo e Pusher

Instale os pacotes necessários:

```bash
npm install --save laravel-echo pusher-js
```

#### Passo 2: Configurar Laravel Echo

Configure Laravel Echo para usar Pusher ou Laravel Echo Server:

```js
import Echo from 'laravel-echo';
window.Pusher = require('pusher-js');

window.Echo = new Echo({
    broadcaster: 'pusher',
    key: process.env.MIX_PUSHER_APP_KEY,
    cluster: process.env.MIX_PUSHER_APP_CLUSTER,
    forceTLS: true
});
```

#### Passo 3: Escutar Eventos

Use Echo para escutar os eventos broadcasted:

```js
window.Echo.channel('chat')
    .listen('MessageSent', (e) => {
        console.log(e.message);
    });
```

## Parametrização Avançada

### Configuração de Canais Públicos e Privados

#### Canal Público

Para um canal público, a configuração é simples e não requer autenticação:

```js
window.Echo.channel('public-channel')
    .listen('PublicEvent', (e) => {
        console.log(e);
    });
```

#### Canal Privado

Para um canal privado, configure a autenticação no backend e no frontend:

```js
window.Echo.private('private-channel')
    .listen('PrivateEvent', (e) => {
        console.log(e);
    });
```

#### Canal de Presença

Para um canal de presença, onde você pode saber quais usuários estão online:

```js
window.Echo.join('presence-channel')
    .here((users) => {
        console.log(users);
    })
    .joining((user) => {
        console.log(user.name + ' joined the channel.');
    })
    .leaving((user) => {
        console.log(user.name + ' left the channel.');
    });
```

## Resumo

A parametrização correta do backend e frontend é essencial para garantir uma comunicação eficiente e segura através de Websockets. Com o Laravel, você pode facilmente configurar e gerenciar eventos broadcasted, autenticação de canais e integração frontend para criar aplicações interativas e responsivas.
