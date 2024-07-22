# Notificações Multicanal

Enviar notificações em múltiplos canais é uma prática comum para garantir que os usuários recebam mensagens importantes através de seus canais preferidos. Laravel facilita o envio de notificações em múltiplos canais, permitindo que você configure notificações para serem enviadas via email, SMS, push notifications, e mais.

## Envio de Notificações em Múltiplos Canais Simultaneamente

### Passo 1: Definir os Canais de Notificação

Quando você cria uma notificação, você pode definir múltiplos canais no método `via`. Por exemplo, você pode enviar uma notificação via email e SMS simultaneamente.

#### Exemplo de Notificação Multicanal

Crie uma notificação usando o comando Artisan:

```bash
php artisan make:notification MultiChannelNotification
```

No arquivo gerado `app/Notifications/MultiChannelNotification.php`, configure a notificação:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Messages\NexmoMessage;
use Illuminate\Notifications\Notification;

class MultiChannelNotification extends Notification
{
    use Queueable;

    private $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function via($notifiable)
    {
        return ['mail', 'nexmo'];
    }

    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Notification Subject')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }

    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content($this->details['body']);
    }
}
```

### Passo 2: Enviar a Notificação

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\MultiChannelNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new MultiChannelNotification($details));
```

## Configuração de Preferências de Notificação do Usuário

### Passo 1: Adicionar Preferências ao Modelo de Usuário

Adicione campos ao modelo de usuário para armazenar as preferências de notificação. Por exemplo:

```php
Schema::table('users', function (Blueprint $table) {
    $table->boolean('notify_via_email')->default(true);
    $table->boolean('notify_via_sms')->default(false);
});
```

### Passo 2: Ajustar a Notificação para Respeitar as Preferências

No método `via` da notificação, ajuste a lógica para enviar a notificação apenas através dos canais preferidos pelo usuário:

```php
public function via($notifiable)
{
    $channels = [];

    if ($notifiable->notify_via_email) {
        $channels[] = 'mail';
    }

    if ($notifiable->notify_via_sms) {
        $channels[] = 'nexmo';
    }

    return $channels;
}
```

### Passo 3: Atualizar Preferências do Usuário

Permita que os usuários atualizem suas preferências de notificação através de um formulário na aplicação:

```php
public function updatePreferences(Request $request)
{
    $user = Auth::user();
    $user->notify_via_email = $request->input('notify_via_email');
    $user->notify_via_sms = $request->input('notify_via_sms');
    $user->save();

    return back()->with('status', 'Preferences updated!');
}
```

## Exemplo Prático

### Backend

Crie uma notificação multicanal e ajuste o modelo de usuário para incluir preferências de notificação.

### Frontend

Adicione um formulário para permitir que os usuários atualizem suas preferências de notificação.

```html
<form action="{{ route('updatePreferences') }}" method="POST">
    @csrf
    <label>
        <input type="checkbox" name="notify_via_email" {{ auth()->user()->notify_via_email ? 'checked' : '' }}>
        Notify via Email
    </label>
    <label>
        <input type="checkbox" name="notify_via_sms" {{ auth()->user()->notify_via_sms ? 'checked' : '' }}>
        Notify via SMS
    </label>
    <button type="submit">Update Preferences</button>
</form>
```

## Resumo

Notificações multicanal no Laravel permitem que você atinja seus usuários através de diversos canais, respeitando suas preferências individuais. Com uma configuração adequada e personalização, você pode garantir que suas notificações sejam entregues de forma eficaz e oportuna.
