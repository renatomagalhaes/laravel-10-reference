# Canal Público - Implementação

Os canais públicos são uma maneira eficiente de transmitir informações a todos os clientes conectados sem a necessidade de autenticação. Este guia mostra como implementar canais públicos usando Websockets no Laravel.

## O que é um Canal Público?

Um canal público é um canal WebSocket acessível a todos os usuários conectados, sem qualquer restrição de acesso. É ideal para enviar atualizações ou notificações gerais que todos os clientes devem receber.

### Benefícios dos Canais Públicos

- **Acesso Livre:** Não requer autenticação, facilitando a implementação.
- **Ampla Divulgação:** Ideal para anúncios e notificações gerais.
- **Simples de Configurar:** Menos complexidade na configuração comparado a canais privados ou de presença.

## Configurando Canais Públicos no Laravel

### Passo 1: Definir um Evento

Crie um evento que será transmitido publicamente. Use o comando Artisan para gerar o evento:

```bash
php artisan make:event PublicMessage
```

Defina o evento para ser broadcasted no arquivo `app/Events/PublicMessage.php`:

```php
namespace App\Events;

use Illuminate\Broadcasting\Channel;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;
use Illuminate\Queue\SerializesModels;

class PublicMessage implements ShouldBroadcast
{
    use InteractsWithSockets, SerializesModels;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function broadcastOn()
    {
        return new Channel('public-channel');
    }
}
```

### Passo 2: Enviar o Evento

Dispare o evento em seu controlador ou outro local apropriado:

```php
use App\Events\PublicMessage;

public function sendMessage(Request $request)
{
    $message = $request->input('message');
    event(new PublicMessage($message));

    return response()->json(['status' => 'Message Sent!']);
}
```

### Passo 3: Configurar o Frontend

No frontend, configure Laravel Echo para escutar o canal público:

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

#### Escutar o Canal Público

Use Echo para escutar os eventos broadcasted no canal público:

```js
window.Echo.channel('public-channel')
    .listen('PublicMessage', (e) => {
        console.log(e.message);
    });
```

### Exemplo Prático

#### Backend

Crie um controlador para enviar mensagens públicas:

```php
namespace App\Http\Controllers;

use App\Events\PublicMessage;
use Illuminate\Http\Request;

class MessageController extends Controller
{
    public function send(Request $request)
    {
        $message = $request->input('message');
        event(new PublicMessage($message));

        return response()->json(['status' => 'Message Sent!']);
    }
}
```

Defina a rota no `routes/web.php`:

```php
Route::post('/send-message', [MessageController::class, 'send']);
```

#### Frontend

Crie um script JavaScript para escutar o canal público:

```js
window.Echo.channel('public-channel')
    .listen('PublicMessage', (e) => {
        alert('Nova mensagem: ' + e.message);
    });
```

## Resumo

Os canais públicos são uma forma simples e eficaz de transmitir informações a todos os clientes conectados sem a necessidade de autenticação. Usando o Laravel, você pode facilmente configurar e gerenciar canais públicos para enviar atualizações e notificações gerais a todos os usuários.
