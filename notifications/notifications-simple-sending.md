# Envio de Notificações Simples

Enviar notificações simples no Laravel é um processo direto que pode ser feito usando diferentes canais como email, SMS e outros. Este guia aborda como configurar e enviar notificações simples usando o Laravel.

## Envio de Notificações via Email

Enviar notificações via email é uma das formas mais comuns de notificar os usuários. O Laravel facilita isso com uma interface simples para definir e enviar notificações.

### Exemplo de Notificação por Email

Crie uma notificação usando o comando Artisan:

```bash
php artisan make:notification EmailNotification
```

No arquivo gerado `app/Notifications/EmailNotification.php`, configure a notificação:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class EmailNotification extends Notification
{
    use Queueable;

    private $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Notification Subject')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }
}
```

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\EmailNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test email notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new EmailNotification($details));
```

## Envio de Notificações via SMS

Além de emails, o Laravel permite enviar notificações via SMS usando serviços como Nexmo (Vonage) ou Twilio.

### Exemplo de Notificação por SMS

Crie uma notificação usando o comando Artisan:

```bash
php artisan make:notification SMSNotification
```

No arquivo gerado `app/Notifications/SMSNotification.php`, configure a notificação:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\NexmoMessage;
use Illuminate\Notifications\Notification;

class SMSNotification extends Notification
{
    use Queueable;

    private $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function via($notifiable)
    {
        return ['nexmo'];
    }

    public function toNexmo($notifiable)
    {
        return (new NexmoMessage)
                    ->content($this->details['body']);
    }
}
```

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\SMSNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test SMS notification',
];

$user->notify(new SMSNotification($details));
```

## Envio de Notificações via Push Notifications

Enviar notificações push requer a integração com serviços como Firebase Cloud Messaging (FCM). Configure o FCM no Laravel e utilize uma biblioteca como `laravel-notification-channels/fcm`.

### Exemplo de Notificação Push

Instale o pacote FCM:

```bash
composer require laravel-notification-channels/fcm
```

Configure o serviço no arquivo `config/services.php`:

```php
'fcm' => [
    'key' => env('FCM_SERVER_KEY'),
],
```

No arquivo `.env`, adicione sua chave do servidor FCM:

```env
FCM_SERVER_KEY=your-fcm-server-key
```

Crie uma notificação usando o comando Artisan:

```bash
php artisan make:notification PushNotification
```

No arquivo gerado `app/Notifications/PushNotification.php`, configure a notificação:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\FcmMessage;
use Illuminate\Notifications\Notification;

class PushNotification extends Notification
{
    use Queueable;

    private $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function via($notifiable)
    {
        return ['fcm'];
    }

    public function toFcm($notifiable)
    {
        return (new FcmMessage)
                    ->data(['message' => $this->details['body']])
                    ->notification(['title' => 'Notification Title', 'body' => $this->details['body']]);
    }
}
```

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\PushNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test push notification',
];

$user->notify(new PushNotification($details));
```

## Resumo

Enviar notificações simples no Laravel é um processo direto e flexível. Com suporte para múltiplos canais como email, SMS e push notifications, o Laravel facilita a entrega de mensagens importantes aos seus usuários de maneira eficaz e eficiente.
