# Trabalhando com RabbitMQ

RabbitMQ é um broker de mensagens amplamente utilizado para comunicação entre serviços em sistemas distribuídos. Este guia aborda como integrar eventos no Laravel com RabbitMQ para gerenciar filas de mensagens.

## Configuração Inicial

### Passo 1: Instalar o Pacote Laravel Queue RabbitMQ

Para usar RabbitMQ no Laravel, primeiro instale o pacote `vladimir-yuldashev/laravel-queue-rabbitmq`:

```bash
composer require vladimir-yuldashev/laravel-queue-rabbitmq
```

### Passo 2: Configurar o Arquivo queue.php

No arquivo `config/queue.php`, adicione a configuração para RabbitMQ:

```php
'connections' => [
    'rabbitmq' => [
        'driver' => 'rabbitmq',
        'host' => env('RABBITMQ_HOST', '127.0.0.1'),
        'port' => env('RABBITMQ_PORT', 5672),
        'vhost' => env('RABBITMQ_VHOST', '/'),
        'login' => env('RABBITMQ_LOGIN', 'guest'),
        'password' => env('RABBITMQ_PASSWORD', 'guest'),
        'queue' => env('RABBITMQ_QUEUE', 'default'),
        'options' => [
            'exchange' => [
                'name' => env('RABBITMQ_EXCHANGE_NAME', 'exchange'),
                'type' => env('RABBITMQ_EXCHANGE_TYPE', 'direct'),
                'durable' => env('RABBITMQ_EXCHANGE_DURABLE', true),
            ],
        ],
    ],
],
```

### Passo 3: Configurar Credenciais do RabbitMQ

Adicione as credenciais do RabbitMQ ao arquivo `.env`:

```env
RABBITMQ_HOST=127.0.0.1
RABBITMQ_PORT=5672
RABBITMQ_VHOST=/
RABBITMQ_LOGIN=guest
RABBITMQ_PASSWORD=guest
RABBITMQ_QUEUE=default
RABBITMQ_EXCHANGE_NAME=exchange
RABBITMQ_EXCHANGE_TYPE=direct
RABBITMQ_EXCHANGE_DURABLE=true
```

## Criando e Despachando Eventos com RabbitMQ

### Passo 1: Criar um Evento

Crie um evento usando o comando Artisan:

```bash
php artisan make:event RabbitMQEvent
```

Defina o evento em `app/Events/RabbitMQEvent.php`:

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;
use Illuminate\Contracts\Queue\ShouldQueue;

class RabbitMQEvent implements ShouldQueue
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }
}
```

### Passo 2: Criar um Listener

Crie um listener para o evento usando o comando Artisan:

```bash
php artisan make:listener RabbitMQEventListener
```

Defina o listener em `app/Listeners/RabbitMQEventListener.php`:

```php
namespace App\Listeners;

use App\Events\RabbitMQEvent;
use Illuminate\Support\Facades\Log;

class RabbitMQEventListener
{
    public function handle(RabbitMQEvent $event)
    {
        // Lógica para processar o evento
        Log::info('Evento RabbitMQ recebido', ['data' => $event->data]);
    }
}
```

### Passo 3: Registrar o Listener

Registre o listener no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\RabbitMQEvent' => [
        'App\Listeners\RabbitMQEventListener',
    ],
];
```

### Passo 4: Despachar o Evento

Para despachar o evento RabbitMQ, use o método `event`:

```php
use App\Events\RabbitMQEvent;

class RabbitMQController extends Controller
{
    public function triggerRabbitMQEvent()
    {
        $data = 'Dados para RabbitMQ';
        event(new RabbitMQEvent($data));

        return response()->json(['message' => 'Evento RabbitMQ disparado com sucesso!']);
    }
}
```

## Monitoramento e Gestão de Filas RabbitMQ

### Verificar Mensagens no RabbitMQ

Utilize a interface de gerenciamento do RabbitMQ para monitorar o status das mensagens e verificar se os eventos estão sendo processados corretamente.

### Ferramentas de Monitoramento

Ferramentas como Prometheus e Grafana podem ser integradas para fornecer uma visão detalhada do desempenho e do estado das suas filas RabbitMQ.

### Retentativas e Backoff Exponencial

Implemente retentativas e backoff exponencial para lidar com falhas temporárias na comunicação com RabbitMQ.

```php
public function handle(RabbitMQEvent $event)
{
    $response = Http::retry(5, 100)->post('https://api.external-service.com/endpoint', [
        'data' => $event->data,
    ]);

    if ($response->failed()) {
        Log::error('Falha na integração com RabbitMQ', ['response' => $response->body()]);
    } else {
        Log::info('Integração com RabbitMQ bem-sucedida', ['response' => $response->body()]);
    }
}
```

## Resumo

Integrar eventos no Laravel com RabbitMQ permite que sua aplicação se comunique eficientemente com outros serviços e sistemas, gerenciando filas de mensagens de forma robusta e escalável. Com a configuração adequada e as práticas recomendadas, você pode garantir uma integração eficaz e segura com RabbitMQ.
