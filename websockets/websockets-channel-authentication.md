# Autenticação de Canal

A autenticação de canal é crucial para garantir que apenas usuários autorizados possam acessar canais privados e de presença. Este guia mostra como implementar a autenticação de canal usando Websockets no Laravel.

## O que é Autenticação de Canal?

A autenticação de canal é o processo de verificar se um usuário tem permissão para acessar um canal específico. É usada para proteger canais privados e de presença, garantindo que apenas usuários autenticados possam escutar eventos nesses canais.

### Benefícios da Autenticação de Canal

- **Segurança:** Garante que apenas usuários autorizados possam acessar dados sensíveis.
- **Controle de Acesso:** Permite a implementação de regras de acesso específicas para diferentes canais.
- **Privacidade:** Protege a comunicação em canais privados e de presença.

## Configurando a Autenticação de Canal no Laravel

### Passo 1: Configurar Rotas de Canal

Defina as rotas de autenticação de canal no arquivo `routes/channels.php`:

```php
Broadcast::channel('private-channel.{id}', function ($user, $id) {
    return (int) $user->id === (int) $id;
});

Broadcast::channel('presence-channel', function ($user) {
    return ['id' => $user->id, 'name' => $user->name];
});
```

### Passo 2: Configurar o Broadcast Driver

Certifique-se de que o driver de broadcast esteja configurado corretamente no arquivo `.env`:

```env
BROADCAST_DRIVER=redis
```

### Passo 3: Configurar o Backend

No backend, configure os eventos para serem broadcasted em canais privados e de presença:

#### Evento de Canal Privado

```php
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PrivateChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Queue\SerializesModels;

class PrivateMessage implements ShouldBroadcast
{
    use InteractsWithSockets, SerializesModels;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function broadcastOn()
    {
        return new PrivateChannel('private-channel.' . auth()->id());
    }
}
```

#### Evento de Canal de Presença

```php
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Broadcasting\PresenceChannel;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Queue\SerializesModels;

class PresenceMessage implements ShouldBroadcast
{
    use InteractsWithSockets, SerializesModels;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function broadcastOn()
    {
        return new PresenceChannel('presence-channel');
    }
}
```

### Passo 4: Configurar o Frontend

No frontend, configure Laravel Echo para escutar os canais autenticados:

#### Configurar Laravel Echo

Configure Laravel Echo no arquivo JavaScript principal, geralmente `resources/js/bootstrap.js`:

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

#### Escutar Canal Privado

Use Echo para escutar os eventos broadcasted no canal privado:

```js
window.Echo.private('private-channel.' + userId)
    .listen('PrivateMessage', (e) => {
        console.log(e.message);
    });
```

#### Escutar Canal de Presença

Use Echo para escutar os eventos broadcasted no canal de presença e monitorar os usuários conectados:

```js
window.Echo.join('presence-channel')
    .here((users) => {
        console.log('Usuários conectados:', users);
    })
    .joining((user) => {
        console.log(user.name + ' entrou no canal.');
    })
    .leaving((user) => {
        console.log(user.name + ' saiu do canal.');
    });
```

### Passo 5: Proteger as Rotas

Proteja as rotas que disparam eventos para garantir que apenas usuários autenticados possam acessá-las:

```php
Route::middleware('auth')->post('/send-private-message', [MessageController::class, 'send']);
Route::middleware('auth')->post('/send-presence-message', [MessageController::class, 'send']);
```

## Resumo

A autenticação de canal no Laravel é essencial para garantir que apenas usuários autorizados possam acessar canais privados e de presença. Usando as ferramentas e configurações apropriadas, você pode proteger a comunicação em sua aplicação e garantir a segurança dos dados transmitidos via Websockets.
