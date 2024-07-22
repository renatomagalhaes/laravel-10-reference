# Canal de Presença - Implementação

Os canais de presença são uma extensão dos canais privados que permitem monitorar os usuários conectados em tempo real. Este guia mostra como implementar canais de presença usando Websockets no Laravel.

## O que é um Canal de Presença?

Um canal de presença é um tipo de canal WebSocket que, além de autenticação, permite rastrear a presença dos usuários conectados. Ele é útil para funcionalidades como listas de usuários online, colaboração em tempo real e mais.

### Benefícios dos Canais de Presença

- **Monitoramento de Presença:** Permite rastrear e exibir a presença dos usuários em tempo real.
- **Interatividade:** Facilita a criação de funcionalidades colaborativas e interativas.
- **Segurança:** Oferece as mesmas garantias de segurança dos canais privados com monitoramento adicional.

## Configurando Canais de Presença no Laravel

### Passo 1: Definir um Evento

Crie um evento que será transmitido em um canal de presença. Use o comando Artisan para gerar o evento:

```bash
php artisan make:event PresenceMessage
```

Defina o evento para ser broadcasted no arquivo `app/Events/PresenceMessage.php`:

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

### Passo 2: Configurar Autenticação de Canal

Configure a autenticação no arquivo `routes/channels.php`:

```php
Broadcast::channel('presence-channel', function ($user) {
    return Auth::check() ? ['id' => $user->id, 'name' => $user->name] : null;
});
```

### Passo 3: Enviar o Evento

Dispare o evento em seu controlador ou outro local apropriado:

```php
use App\Events\PresenceMessage;

public function sendMessage(Request $request)
{
    $message = $request->input('message');
    event(new PresenceMessage($message));

    return response()->json(['status' => 'Message Sent!']);
}
```

### Passo 4: Configurar o Frontend

No frontend, configure Laravel Echo para escutar o canal de presença:

#### Instalar Laravel Echo

Instale Laravel Echo e Pusher, se ainda não o fez:

```bash
npm install --save laravel-echo pusher-js
```

#### Configurar Laravel Echo

Configure Laravel Echo no arquivo JavaScript principal, geralmente `resources/js/bootstrap.js`:

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

#### Escutar o Canal de Presença

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

### Exemplo Prático

#### Backend

Crie um controlador para enviar mensagens de presença:

```php
namespace App\Http\Controllers;

use App\Events\PresenceMessage;
use Illuminate\Http\Request;

class MessageController extends Controller
{
    public function send(Request $request)
    {
        $message = $request->input('message');
        event(new PresenceMessage($message));

        return response()->json(['status' => 'Message Sent!']);
    }
}
```

Defina a rota no `routes/web.php`:

```php
Route::post('/send-presence-message', [MessageController::class, 'send']);
```

#### Frontend

Crie um script JavaScript para escutar o canal de presença e monitorar a presença dos usuários:

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

## Resumo

Os canais de presença oferecem uma maneira eficaz de monitorar a presença dos usuários em tempo real, facilitando a criação de funcionalidades colaborativas e interativas. Usando o Laravel, você pode configurar e gerenciar canais de presença para rastrear e exibir os usuários conectados, garantindo uma experiência de usuário rica e envolvente.
