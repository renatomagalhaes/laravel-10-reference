# Trabalhando com Amazon SES para envio de email

Amazon Simple Email Service (SES) é uma plataforma escalável e econômica para envio de emails transacionais e de marketing. Este guia aborda como configurar e usar o Amazon SES no Laravel para enviar notificações por email.

## Configurando Amazon SES no Laravel

### Passo 1: Registrar uma Conta no Amazon SES

Primeiro, registre-se no Amazon SES e verifique seu domínio ou endereço de email.

### Passo 2: Instalar o Pacote AWS SDK

Instale o pacote AWS SDK via Composer:

```bash
composer require aws/aws-sdk-php
```

### Passo 3: Configurar o Amazon SES no Laravel

No arquivo `.env`, adicione suas credenciais do SES:

```env
MAIL_MAILER=ses
AWS_ACCESS_KEY_ID=your_access_key_id
AWS_SECRET_ACCESS_KEY=your_secret_access_key
AWS_DEFAULT_REGION=us-east-1
MAIL_FROM_ADDRESS=example@example.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Passo 4: Configurar o Serviço de Email

No arquivo `config/mail.php`, configure o Amazon SES como o serviço de email:

```php
return [
    'default' => env('MAIL_MAILER', 'ses'),

    'mailers' => [
        'ses' => [
            'transport' => 'ses',
        ],
    ],

    'from' => [
        'address' => env('MAIL_FROM_ADDRESS', 'hello@example.com'),
        'name' => env('MAIL_FROM_NAME', 'Example'),
    ],
];
```

## Enviando Notificações com Amazon SES

### Passo 1: Criar uma Notificação

Use o comando Artisan para criar uma notificação:

```bash
php artisan make:notification SESNotification
```

### Passo 2: Configurar a Notificação

No arquivo `app/Notifications/SESNotification.php`, configure a notificação para usar Amazon SES:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class SESNotification extends Notification
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
                    ->subject('SES Notification')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }
}
```

### Passo 3: Enviar a Notificação

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\SESNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test notification using Amazon SES',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new SESNotification($details));
```

## Monitoramento e Relatórios

### Passo 1: Configurar Relatórios no Amazon SES

No painel do SES, configure uma SNS Topic para receber notificações de entrega, bounces e reclamações.

### Passo 2: Criar uma Rota para Webhooks

No arquivo `routes/web.php`, adicione uma rota para receber notificações de eventos:

```php
Route::post('/webhook/ses', 'WebhookController@handleSESWebhook');
```

### Passo 3: Implementar o Controlador de Webhook

Crie um controlador para processar notificações de eventos:

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class WebhookController extends Controller
{
    public function handleSESWebhook(Request $request)
    {
        $events = json_decode($request->getContent(), true);

        foreach ($events['Records'] as $event) {
            Log::info('SES event: ', $event);

            // Processar o evento
            // ...
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Exemplo Prático

### Backend

1. Configure Amazon SES no Laravel.
2. Crie e envie notificações usando Amazon SES.
3. Ative e processe webhooks para monitorar eventos de email.

### Frontend

No frontend, você pode criar interfaces administrativas para visualizar os relatórios de envio e eventos, como aberturas, cliques e bounces.

## Resumo

Trabalhar com Amazon SES no Laravel permite enviar notificações de email de forma eficiente e monitorar a entrega através de relatórios detalhados. Com a configuração adequada e a implementação de webhooks, você pode garantir a entrega bem-sucedida e o monitoramento eficaz das suas notificações.
