# Controle de Taxa de Envio

Controlar a taxa de envio de notificações é crucial para evitar sobrecarregar os servidores de email e garantir que suas mensagens sejam entregues de forma eficiente. Este guia aborda como implementar o controle de taxa de envio no Laravel.

## Importância do Controle de Taxa de Envio

### Benefícios

- **Evita Bloqueios:** Reduz a chance de seus emails serem bloqueados por provedores de email.
- **Gerencia Recursos:** Distribui a carga de envio de forma eficiente ao longo do tempo.
- **Melhora a Entregabilidade:** Aumenta a chance de seus emails serem entregues na caixa de entrada.

## Implementando Controle de Taxa de Envio no Laravel

### Passo 1: Configurar o Driver de Filas

Certifique-se de que você tenha configurado um driver de filas apropriado no arquivo `.env`. Para usar Redis, por exemplo:

```env
QUEUE_CONNECTION=redis
```

### Passo 2: Criar uma Notificação com Rate Limiting

Use o comando Artisan para criar uma notificação:

```bash
php artisan make:notification RateLimitedNotification
```

### Passo 3: Configurar a Notificação para Usar Filas

No arquivo `app/Notifications/RateLimitedNotification.php`, configure a notificação para usar filas e implementar o controle de taxa de envio:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;
use Illuminate\Support\Facades\RateLimiter;

class RateLimitedNotification extends Notification
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
        if ($this->shouldSend($notifiable)) {
            return (new MailMessage)
                        ->subject('Rate Limited Notification')
                        ->line($this->details['body'])
                        ->action($this->details['actionText'], $this->details['actionURL'])
                        ->line('Thank you for using our application!');
        }
    }

    protected function shouldSend($notifiable)
    {
        return RateLimiter::attempt(
            'send-notification:' . $notifiable->id,
            5, // Allow 5 notifications
            60 // per 60 seconds
        );
    }
}
```

### Passo 4: Configurar Rate Limiting no Laravel

No arquivo `config/app.php`, certifique-se de que a configuração do Rate Limiter esteja definida:

```php
'rate_limiter' => [
    'max_attempts' => 5,
    'decay_minutes' => 1,
],
```

### Passo 5: Enviar a Notificação

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\RateLimitedNotification;
use App\Models\User;

$details = [
    'body' => 'This is a rate limited notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$users = User::all();

foreach ($users as $user) {
    $user->notify(new RateLimitedNotification($details));
}
```

### Monitoramento e Ajuste

### Passo 1: Monitorar o Uso de Taxa

Monitore o uso de taxa e ajuste os limites conforme necessário. Use ferramentas como Laravel Horizon para monitorar filas e ver o status das notificações.

```bash
composer require laravel/horizon
php artisan horizon:install
php artisan migrate
php artisan horizon
```

Acesse o painel do Horizon em `http://your-app/horizon`.

### Passo 2: Ajustar os Limites de Taxa

Com base nos dados de monitoramento, ajuste os limites de taxa para otimizar a entrega de notificações e evitar bloqueios.

```php
RateLimiter::for('send-notification', function (Request $request) {
    return Limit::perMinute(10)->by($request->user()->id);
});
```

## Exemplo Prático

### Backend

1. Configure o driver de filas e o rate limiter.
2. Crie uma notificação com controle de taxa de envio.
3. Monitore e ajuste os limites conforme necessário.

### Frontend

No frontend, você pode criar interfaces administrativas para visualizar os logs e as estatísticas de entrega e ajuste dos limites de taxa.

## Resumo

Implementar o controle de taxa de envio é essencial para garantir que suas notificações sejam entregues de forma eficiente e para evitar sobrecarregar os servidores de email. Com a configuração adequada e o uso de filas e rate limiting, você pode otimizar a entrega de suas notificações e melhorar a experiência do usuário.
