# Notificações em Realtime

Notificações em tempo real permitem que os usuários recebam atualizações instantâneas sobre eventos importantes na aplicação. Este guia mostra como implementar notificações em tempo real usando Websockets no Laravel.

## O que são Notificações em Realtime?

Notificações em tempo real são mensagens enviadas instantaneamente para os usuários quando determinados eventos ocorrem na aplicação. Elas são úteis para manter os usuários informados e engajados sem a necessidade de recarregar a página.

### Benefícios das Notificações em Realtime

- **Interatividade:** Melhora a experiência do usuário com atualizações instantâneas.
- **Engajamento:** Mantém os usuários engajados e informados sobre eventos importantes.
- **Eficiência:** Reduz a latência, fornecendo informações imediatamente.

## Configurando Notificações em Realtime no Laravel

### Passo 1: Configurar o Broadcast Driver

Certifique-se de que o driver de broadcasting esteja configurado corretamente no arquivo `.env`:

```env
BROADCAST_DRIVER=redis
```

### Passo 2: Criar uma Notificação

Use o comando Artisan para criar uma nova notificação:

```bash
php artisan make:notification RealtimeNotification
```

### Passo 3: Definir a Notificação

Defina a notificação para ser broadcasted no arquivo `app/Notifications/RealtimeNotification.php`:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\BroadcastMessage;

class RealtimeNotification extends Notification implements ShouldQueue
{
    use Queueable;

    public $message;

    public function __construct($message)
    {
        $this->message = $message;
    }

    public function via($notifiable)
    {
        return ['broadcast'];
    }

    public function toBroadcast($notifiable)
    {
        return new BroadcastMessage([
            'message' => $this->message,
        ]);
    }
}
```

### Passo 4: Enviar a Notificação

Envie a notificação para os usuários:

```php
use App\Notifications\RealtimeNotification;

public function notifyUser(Request $request)
{
    $user = User::find($request->input('user_id'));
    $message = $request->input('message');

    $user->notify(new RealtimeNotification($message));

    return response()->json(['status' => 'Notification Sent!']);
}
```

### Passo 5: Configurar o Frontend

No frontend, configure Laravel Echo para escutar as notificações broadcasted.

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

#### Escutar as Notificações

Use Echo para escutar as notificações broadcasted:

```js
window.Echo.private('App.Models.User.' + userId)
    .notification((notification) => {
        console.log(notification.message);
        alert('Nova notificação: ' + notification.message);
    });
```

### Exemplo Prático

#### Backend

Crie um controlador para enviar notificações:

```php
namespace App\Http\Controllers;

use App\Notifications\RealtimeNotification;
use Illuminate\Http\Request;
use App\Models\User;

class NotificationController extends Controller
{
    public function send(Request $request)
    {
        $user = User::find($request->input('user_id'));
        $message = $request->input('message');

        $user->notify(new RealtimeNotification($message));

        return response()->json(['status' => 'Notification Sent!']);
    }
}
```

Defina a rota no `routes/web.php`:

```php
Route::post('/send-notification', [NotificationController::class, 'send']);
```

#### Frontend

Crie um script JavaScript para escutar as notificações:

```js
window.Echo.private('App.Models.User.' + userId)
    .notification((notification) => {
        alert('Nova notificação: ' + notification.message);
    });
```

## Resumo

Notificações em tempo real no Laravel permitem que os usuários recebam atualizações instantâneas sobre eventos importantes na aplicação. Usando o sistema de notificações do Laravel e Websockets, você pode facilmente configurar e gerenciar notificações em tempo real para fornecer uma experiência de usuário rica e interativa.
