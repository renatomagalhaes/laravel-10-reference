# Envio de Notificações em Lote

Enviar notificações em lote é uma prática comum para alcançar um grande número de usuários simultaneamente. Laravel facilita o envio de notificações em massa usando filas para garantir que o processo seja eficiente e não sobrecarregue o servidor.

## Benefícios do Envio em Lote

- **Eficiência:** Processa grandes volumes de notificações de maneira eficiente.
- **Escalabilidade:** Usa filas para distribuir a carga de envio ao longo do tempo.
- **Gerenciamento de Recursos:** Minimiza o impacto no desempenho do servidor durante picos de envio.

## Configurando o Envio de Notificações em Lote

### Passo 1: Configurar o Driver de Filas

Certifique-se de que você tenha configurado um driver de filas apropriado no arquivo `.env`. Para usar Redis, por exemplo:

```env
QUEUE_CONNECTION=redis
```

### Passo 2: Criar uma Notificação para Envio em Lote

Crie uma notificação que será enviada em lote:

```bash
php artisan make:notification BulkNotification
```

Configure a notificação em `app/Notifications/BulkNotification.php` para usar filas:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class BulkNotification extends Notification
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
                    ->subject('Bulk Notification')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }
}
```

### Passo 3: Enviar Notificações em Lote

Use o método `notify` em um conjunto de usuários e adicione a notificação à fila:

```php
use App\Notifications\BulkNotification;
use App\Models\User;

$details = [
    'body' => 'This is a bulk notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$users = User::all();

foreach ($users as $user) {
    $user->notify(new BulkNotification($details));
}
```

### Passo 4: Configurar o Worker de Filas

Inicie um worker de filas para processar as notificações em segundo plano:

```bash
php artisan queue:work
```

## Otimização do Envio em Lote

### Uso de Chunks

Para melhorar a eficiência, envie notificações em chunks (lotes menores) em vez de processar todos os usuários de uma vez. Isso reduz a carga no banco de dados e no servidor.

```php
$details = [
    'body' => 'This is a bulk notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

User::chunk(100, function ($users) use ($details) {
    foreach ($users as $user) {
        $user->notify(new BulkNotification($details));
    }
});
```

### Paralelismo

Configure múltiplos workers para processar filas em paralelo, aumentando a taxa de envio de notificações. No arquivo `supervisord.conf`, adicione múltiplos processos:

```conf
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/artisan queue:work --sleep=3 --tries=3
autostart=true
autorestart=true
user=your-username
numprocs=5
redirect_stderr=true
stdout_logfile=/path/to/your/logfile.log
```

### Monitoramento

Use ferramentas como Laravel Horizon para monitorar e gerenciar filas de forma eficaz.

```bash
composer require laravel/horizon
php artisan horizon:install
php artisan migrate
php artisan horizon
```

Acesse o painel do Horizon em `http://your-app/horizon`.

## Resumo

O envio de notificações em lote no Laravel é um processo eficiente que pode ser otimizado usando chunks, paralelismo e monitoramento de filas. Com uma configuração adequada, você pode enviar grandes volumes de notificações de forma eficiente e confiável.
