# Canal Privado - Implementação

Os canais privados fornecem uma camada adicional de segurança ao exigir autenticação antes de permitir que os usuários escutem eventos. Este guia mostra como implementar canais privados usando Websockets no Laravel.

## O que é um Canal Privado?

Um canal privado é um canal WebSocket que requer autenticação para ser acessado. Ele é usado para transmitir informações sensíveis que devem ser acessíveis apenas a usuários autorizados.

### Benefícios dos Canais Privados

- **Segurança:** Garante que apenas usuários autenticados possam acessar o canal.
- **Privacidade:** Ideal para enviar dados sensíveis ou específicos para usuários individuais.
- **Controle de Acesso:** Facilita a implementação de regras de acesso granulares.

## Configurando Canais Privados no Laravel

### Passo 1: Definir um Evento

Crie um evento que será transmitido em um canal privado. Use o comando Artisan para gerar o evento:

```bash
php artisan make:event PrivateMessage
```

Defina o evento para ser broadcasted no arquivo `app/Events/PrivateMessage.php`:

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
        return new PrivateChannel('private-channel');
    }
}
```

### Passo 2: Configurar Autenticação de Canal

Configure a autenticação no arquivo `routes/channels.php`:

```php
Broadcast::channel('private-channel', function ($user) {
    return Auth::check();
});
```

### Passo 3: Enviar o Evento

Dispare o evento em seu controlador ou outro local apropriado:

```php
use App\Events\PrivateMessage;

public function sendMessage(Request $request)
{
    $message = $request->input('message');
    event(new PrivateMessage($message));

    return response()->json(['status' => 'Message Sent!']);
}
```

### Passo 4: Configurar o Frontend

No frontend, configure Laravel Echo para escutar o canal privado:

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

#### Escutar o Canal Privado

Use Echo para escutar os eventos broadcasted no canal privado:

```js
window.Echo.private('private-channel')
    .listen('PrivateMessage', (e) => {
        console.log(e.message);
    });
```

### Exemplo Prático

#### Backend

Crie um controlador para enviar mensagens privadas:

```php
namespace App\Http\Controllers;

use App\Events\PrivateMessage;
use Illuminate\Http\Request;

class MessageController extends Controller
{
    public function send(Request $request)
    {
        $message = $request->input('message');
        event(new PrivateMessage($message));

        return response()->json(['status' => 'Message Sent!']);
    }
}
```

Defina a rota no `routes/web.php`:

```php
Route::post('/send-private-message', [MessageController::class, 'send']);
```

#### Frontend

Crie um script JavaScript para escutar o canal privado:

```js
window.Echo.private('private-channel')
    .listen('PrivateMessage', (e) => {
        alert('Nova mensagem privada: ' + e.message);
    });
```

## Resumo

Os canais privados oferecem uma camada adicional de segurança, garantindo que apenas usuários autenticados possam acessar o canal. Usando o Laravel, você pode facilmente configurar e gerenciar canais privados para enviar dados sensíveis ou específicos a usuários autorizados.
