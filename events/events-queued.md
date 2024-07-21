# Eventos e Listeners Queued

Enfileirar eventos e listeners no Laravel permite que você processe tarefas de forma assíncrona, melhorando a performance e a capacidade de resposta da aplicação. Este guia aborda como configurar e usar eventos e listeners queued no Laravel.

## Introdução aos Eventos Queued

Enfileirar eventos e listeners permite que você processe tarefas em segundo plano, sem bloquear o fluxo principal da aplicação. Isso é útil para tarefas demoradas, como envio de emails, processamento de imagens, ou qualquer outra operação que não precise ser feita imediatamente.

## Criando Eventos Queued

### Passo 1: Criar um Evento

Primeiro, crie um evento usando o comando Artisan:

```bash
php artisan make:event ExampleQueuedEvent
```

Isso criará um arquivo de evento em `app/Events/ExampleQueuedEvent.php` com a estrutura básica:

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Queue\ShouldQueue;

class ExampleQueuedEvent implements ShouldQueue
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }
}
```

### Passo 2: Despachar o Evento

Para despachar o evento enfileirado, use o método `event`:

```php
use App\Events\ExampleQueuedEvent;

class ExampleController extends Controller
{
    public function triggerQueuedEvent()
    {
        $data = 'Dados do evento enfileirado';
        event(new ExampleQueuedEvent($data));

        return response()->json(['message' => 'Evento enfileirado disparado com sucesso!']);
    }
}
```

## Criando Listeners Queued

### Passo 1: Criar um Listener Queued

Listeners que implementam a interface `ShouldQueue` serão automaticamente enfileirados. Crie um listener enfileirado usando o comando Artisan:

```bash
php artisan make:listener ExampleQueuedListener
```

O comando criará um arquivo de listener em `app/Listeners/ExampleQueuedListener.php` com a estrutura básica:

```php
namespace App\Listeners;

use App\Events\ExampleQueuedEvent;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class ExampleQueuedListener implements ShouldQueue
{
    use InteractsWithQueue;

    public function handle(ExampleQueuedEvent $event)
    {
        // Lógica do listener
    }
}
```

### Passo 2: Registrar o Listener

Registre o listener no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\ExampleQueuedEvent' => [
        'App\Listeners\ExampleQueuedListener',
    ],
];
```

### Passo 3: Configurar a Conexão da Fila

No arquivo `.env`, configure a conexão da fila:

```env
QUEUE_CONNECTION=redis
```

## Configuração de Workers

Para processar os jobs enfileirados, configure os workers no Supervisor:

```ini
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/your/app/artisan queue:work redis --sleep=3 --tries=3
autostart=true
autorestart=true
user=your-username
numprocs=8
redirect_stderr=true
stdout_logfile=/path/to/your/app/worker.log
```

Inicie o worker com o comando Artisan:

```bash
php artisan queue:work redis
```

## Verificando e Retentando Jobs Enfileirados

### Verificar Jobs Enfileirados

Use o comando Artisan para verificar o status dos jobs enfileirados:

```bash
php artisan queue:failed
```

### Retentar Jobs Falhados

Você pode retentar os jobs falhados usando o comando Artisan:

```bash
php artisan queue:retry all
```

## Resumo

Enfileirar eventos e listeners no Laravel permite que você processe tarefas de forma assíncrona, melhorando a performance e a capacidade de resposta da aplicação. Com a configuração adequada e os exemplos fornecidos, você pode começar a implementar eventos e listeners enfileirados na sua aplicação Laravel.
