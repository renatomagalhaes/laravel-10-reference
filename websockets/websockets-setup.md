# Configuração Inicial

Configurar Websockets no Laravel envolve a instalação de pacotes necessários, configuração do servidor e integração com o frontend. Este guia detalha os passos necessários para configurar Websockets em sua aplicação Laravel.

## Passo 1: Instalar o Pacote Laravel Echo e Pusher

Laravel Echo é uma biblioteca JavaScript que facilita a inscrição em canais e a escuta de eventos broadcasted. Pusher é um serviço de Websockets hospedado que fornece uma API simples para Websockets.

### Instalar Laravel Echo

```bash
npm install --save laravel-echo pusher-js
```

### Instalar Pusher PHP Server

```bash
composer require pusher/pusher-php-server
```

## Passo 2: Configurar o Broadcast Driver

Atualize o arquivo de configuração `config/broadcasting.php` para usar o Pusher:

```php
'connections' => [
    'pusher' => [
        'driver' => 'pusher',
        'key' => env('PUSHER_APP_KEY'),
        'secret' => env('PUSHER_APP_SECRET'),
        'app_id' => env('PUSHER_APP_ID'),
        'options' => [
            'cluster' => env('PUSHER_APP_CLUSTER'),
            'useTLS' => true,
        ],
    ],
],
```

## Passo 3: Configurar Credenciais do Pusher

Adicione as credenciais do Pusher ao arquivo `.env`:

```env
PUSHER_APP_ID=your-app-id
PUSHER_APP_KEY=your-app-key
PUSHER_APP_SECRET=your-app-secret
PUSHER_APP_CLUSTER=mt1
```

## Passo 4: Configurar Laravel Echo

Configure Laravel Echo no seu arquivo JavaScript, geralmente `resources/js/bootstrap.js`:

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

## Passo 5: Criar um Evento Broadcast

Crie um evento que será broadcasted usando o Artisan:

```bash
php artisan make:event MessageSent
```

Defina o evento para ser broadcasted no arquivo `app/Events/MessageSent.php`:

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

## Passo 6: Broadcasting do Evento

Broadcast o evento quando uma mensagem for enviada:

```php
use App\Events\MessageSent;

public function sendMessage(Request $request)
{
    $message = $request->input('message');

    event(new MessageSent($message));

    return response()->json(['status' => 'Message Sent!']);
}
```

## Passo 7: Escutar Eventos no Frontend

No seu frontend, escute o evento broadcasted:

```js
window.Echo.channel('chat')
    .listen('MessageSent', (e) => {
        console.log(e.message);
    });
```

## Resumo

Configurar Websockets no Laravel envolve a instalação de pacotes, configuração do broadcast driver, definição de eventos e escuta de eventos no frontend. Seguindo esses passos, você pode integrar Websockets em sua aplicação Laravel, permitindo comunicação em tempo real.
