# Trabalhando com SendGrid

SendGrid é uma plataforma popular para envio de emails em massa e notificações transacionais. Este guia aborda como configurar e usar SendGrid no Laravel para enviar notificações por email de forma eficiente e confiável.

## Configurando SendGrid no Laravel

### Passo 1: Registrar uma Conta no SendGrid

Primeiro, registre-se no SendGrid e obtenha sua chave API.

### Passo 2: Instalar o Pacote SendGrid

Instale o pacote SendGrid via Composer:

```bash
composer require guzzlehttp/guzzle
```

### Passo 3: Configurar o SendGrid no Laravel

No arquivo `.env`, adicione suas credenciais do SendGrid:

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.sendgrid.net
MAIL_PORT=587
MAIL_USERNAME=apikey
MAIL_PASSWORD=your_sendgrid_api_key
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=example@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Passo 4: Configurar o Serviço de Email

No arquivo `config/mail.php`, configure o SendGrid como o serviço de email:

```php
return [
    'default' => env('MAIL_MAILER', 'smtp'),

    'mailers' => [
        'smtp' => [
            'transport' => 'smtp',
            'host' => env('MAIL_HOST', 'smtp.sendgrid.net'),
            'port' => env('MAIL_PORT', 587),
            'encryption' => env('MAIL_ENCRYPTION', 'tls'),
            'username' => env('MAIL_USERNAME'),
            'password' => env('MAIL_PASSWORD'),
            'timeout' => null,
            'auth_mode' => null,
        ],
    ],

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
];
```

## Enviando Notificações com SendGrid

### Passo 1: Criar uma Notificação

Use o comando Artisan para criar uma notificação:

```bash
php artisan make:notification SendGridNotification
```

### Passo 2: Configurar a Notificação

No arquivo `app/Notifications/SendGridNotification.php`, configure a notificação para usar SendGrid:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class SendGridNotification extends Notification
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
                    ->subject('SendGrid Notification')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }
}
```

### Passo 3: Enviar a Notificação

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\SendGridNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test notification using SendGrid',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new SendGridNotification($details));
```

## Monitoramento e Relatórios

### Passo 1: Ativar Relatórios no SendGrid

No painel do SendGrid, ative os relatórios de entrega e bounces. Configure webhooks para receber notificações de eventos de email, como aberturas, cliques, bounces e spam reports.

### Passo 2: Criar uma Rota para Webhooks

No arquivo `routes/web.php`, adicione uma rota para receber notificações de eventos:

```php
Route::post('/webhook/sendgrid', 'WebhookController@handleSendGridWebhook');
```

### Passo 3: Implementar o Controlador de Webhook

Crie um controlador para processar notificações de eventos:

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class WebhookController extends Controller
{
    public function handleSendGridWebhook(Request $request)
    {
        $events = $request->all();

        foreach ($events as $event) {
            Log::info('SendGrid event: ', $event);
            
            // Processar o evento
            // ...
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Exemplo Prático

### Backend

1. Configure SendGrid no Laravel.
2. Crie e envie notificações usando SendGrid.
3. Ative e processe webhooks para monitorar eventos de email.

### Frontend

No frontend, você pode criar interfaces administrativas para visualizar os relatórios de envio e eventos, como aberturas, cliques e bounces.

## Resumo

Trabalhar com SendGrid no Laravel permite enviar notificações de email de forma eficiente e monitorar a entrega através de relatórios detalhados. Com a configuração adequada e a implementação de webhooks, você pode garantir a entrega bem-sucedida e o monitoramento eficaz das suas notificações.
