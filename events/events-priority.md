# Manipulação de Prioridade de Eventos

No Laravel, você pode definir a prioridade dos listeners para controlar a ordem em que eles são executados quando um evento é disparado. Esta funcionalidade é útil quando você precisa garantir que certos listeners sejam executados antes de outros.

## Definindo Prioridade de Listeners

### Passo 1: Criar Listeners

Primeiro, crie dois listeners usando o comando Artisan:

```bash
php artisan make:listener HighPriorityListener
php artisan make:listener LowPriorityListener
```

Isso criará os arquivos `HighPriorityListener.php` e `LowPriorityListener.php` em `app/Listeners` com a estrutura básica.

### Passo 2: Implementar a Lógica dos Listeners

Adicione a lógica necessária nos métodos `handle` dos listeners.

#### HighPriorityListener

```php
namespace App\Listeners;

use App\Events\ExampleEvent;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class HighPriorityListener
{
    public function handle(ExampleEvent $event)
    {
        // Lógica de alta prioridade
        \Log::info('HighPriorityListener executado.');
    }
}
```

#### LowPriorityListener

```php
namespace App\Listeners;

use App\Events\ExampleEvent;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class LowPriorityListener
{
    public function handle(ExampleEvent $event)
    {
        // Lógica de baixa prioridade
        \Log::info('LowPriorityListener executado.');
    }
}
```

### Passo 3: Registrar Listeners com Prioridade

Registre os listeners no arquivo `EventServiceProvider.php`, definindo a prioridade.

```php
protected $listen = [
    'App\Events\ExampleEvent' => [
        [
            'listener' => 'App\Listeners\HighPriorityListener',
            'priority' => 10,
        ],
        [
            'listener' => 'App\Listeners\LowPriorityListener',
            'priority' => 5,
        ],
    ],
];
```

Neste exemplo, `HighPriorityListener` será executado antes de `LowPriorityListener` porque possui uma prioridade mais alta (10 vs 5).

## Exemplo Completo

### Passo 1: Criar o Evento

```bash
php artisan make:event ExampleEvent
```

Defina o evento em `app/Events/ExampleEvent.php`:

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Queue\ShouldQueue;

class ExampleEvent
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }
}
```

### Passo 2: Criar os Listeners

```bash
php artisan make:listener HighPriorityListener
php artisan make:listener LowPriorityListener
```

Defina os listeners em `app/Listeners/HighPriorityListener.php` e `app/Listeners/LowPriorityListener.php`:

#### HighPriorityListener

```php
namespace App\Listeners;

use App\Events\ExampleEvent;

class HighPriorityListener
{
    public function handle(ExampleEvent $event)
    {
        \Log::info('HighPriorityListener executado.');
    }
}
```

#### LowPriorityListener

```php
namespace App\Listeners;

use App\Events\ExampleEvent;

class LowPriorityListener
{
    public function handle(ExampleEvent $event)
    {
        \Log::info('LowPriorityListener executado.');
    }
}
```

### Passo 3: Registrar os Listeners com Prioridade

Registre os listeners no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\ExampleEvent' => [
        [
            'listener' => 'App\Listeners\HighPriorityListener',
            'priority' => 10,
        ],
        [
            'listener' => 'App\Listeners\LowPriorityListener',
            'priority' => 5,
        ],
    ],
];
```

### Passo 4: Despachar o Evento

```php
use App\Events\ExampleEvent;

class ExampleController extends Controller
{
    public function triggerEvent()
    {
        $data = 'Dados do evento';
        event(new ExampleEvent($data));

        return response()->json(['message' => 'Evento disparado com sucesso!']);
    }
}
```

## Resumo

Definir a prioridade dos listeners no Laravel permite controlar a ordem de execução dos listeners quando um evento é disparado. Isso é útil para garantir que tarefas críticas sejam processadas antes de outras tarefas. Com a configuração adequada e os exemplos fornecidos, você pode implementar a manipulação de prioridade de eventos na sua aplicação Laravel.
