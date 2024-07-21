# Tratamento de Falhas em Jobs

Gerenciar falhas em jobs é uma parte crucial do uso de filas no Laravel. Este guia aborda como lidar com falhas, implementar retentativas e definir lógica de fallback para garantir que suas tarefas sejam executadas de maneira confiável.

## Gerenciamento de Falhas

### Verificar Jobs Falhados

O Laravel armazena informações sobre jobs que falharam na tabela `failed_jobs`. Você pode visualizar os jobs falhados usando o comando Artisan:

```bash
php artisan queue:failed
```

### Implementar Retentativas

Você pode configurar o número de tentativas de um job antes de ele ser considerado como falhado. Isso é feito usando a propriedade `tries` no job:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ExampleJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    public $tries = 5; // Número de tentativas

    public function handle()
    {
        // Lógica do job
    }
}
```

### Adicionar Delay entre Retentativas

Você pode adicionar um delay entre as tentativas de um job usando a propriedade `retryAfter`:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ExampleJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    public $tries = 5;
    public $retryAfter = 60; // Delay em segundos entre tentativas

    public function handle()
    {
        // Lógica do job
    }
}
```

## Lógica de Fallback

### Definir Lógica de Fallback

A lógica de fallback é executada quando um job falha permanentemente após todas as tentativas. Você pode definir a lógica de fallback no método `failed` do job:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Log;

class ExampleJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    public $tries = 5;

    public function handle()
    {
        // Lógica do job
        throw new \Exception('Falha proposital para exemplo');
    }

    public function failed(\Exception $exception)
    {
        // Lógica de fallback
        Log::error('Job falhou: ' . $exception->getMessage());
    }
}
```

## Implementação de Dead Letter Queues (DLQ)

### O que são Dead Letter Queues

Dead Letter Queues são filas onde os jobs que falharam após todas as tentativas são movidos. Isso permite uma revisão manual e tratamento específico para esses jobs.

### Configuração de DLQ

Para configurar uma DLQ, você pode usar a funcionalidade de falha do Laravel e configurar uma fila separada para jobs falhados:

```php
// config/queue.php

'failed' => [
    'driver' => env('QUEUE_FAILED_DRIVER', 'database-uuids'),
    'database' => env('DB_CONNECTION', 'mysql'),
    'table' => 'failed_jobs',
],
```

Você pode então processar esses jobs manualmente ou mover para uma fila de retrabalho.

## Monitoramento de Falhas

### Notificações de Falhas

Implemente notificações para alertar os administradores sobre jobs falhados:

```php
use Illuminate\Support\Facades\Notification;
use App\Notifications\JobFailedNotification;

class ExampleJob implements ShouldQueue
{
    // Lógica do job

    public function failed(\Exception $exception)
    {
        Notification::route('mail', 'admin@example.com')->notify(new JobFailedNotification($exception));
    }
}
```

### Exemplo de Notificação

Crie uma classe de notificação para enviar o alerta:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Notifications\Notification;
use Illuminate\Notifications\Messages\MailMessage;

class JobFailedNotification extends Notification implements ShouldQueue
{
    use Queueable;

    protected $exception;

    public function __construct(\Exception $exception)
    {
        $this->exception = $exception;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Job Falhou')
                    ->line('Um job falhou com a seguinte mensagem:')
                    ->line($this->exception->getMessage())
                    ->action('Verificar Jobs Falhados', url('/admin/failed-jobs'))
                    ->line('Obrigado!');
    }
}
```

## Resumo

Gerenciar falhas em jobs é essencial para garantir a confiabilidade e a robustez das tarefas assíncronas no Laravel. Com a configuração correta, incluindo retentativas, lógica de fallback, DLQs e notificações, você pode assegurar que seus jobs sejam processados de forma eficaz e que qualquer problema seja prontamente tratado.
