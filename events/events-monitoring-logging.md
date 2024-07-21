# Monitoramento e Logging de Eventos

Monitorar e registrar eventos no Laravel é crucial para garantir a integridade dos processos, identificar e resolver problemas rapidamente e manter uma visão geral das atividades do sistema. Este guia aborda como configurar e usar ferramentas de monitoramento e logging para eventos no Laravel.

## Importância do Monitoramento e Logging

### Monitoramento
Monitorar eventos permite que você acompanhe o fluxo de dados e identifique problemas antes que eles afetem os usuários. Ferramentas de monitoramento podem fornecer insights sobre desempenho, taxa de falhas e outras métricas importantes.

### Logging
Registrar eventos cria um histórico detalhado das atividades do sistema, útil para depuração, auditoria e análise posterior. Logs detalhados podem ajudar a entender o comportamento do sistema e identificar padrões de erro.

## Configuração de Logging no Laravel

### Passo 1: Configurar o Arquivo de Logging

No arquivo `config/logging.php`, configure os canais de logging. O Laravel suporta múltiplos canais, incluindo `single`, `daily`, `slack`, `papertrail`, `syslog`, e `errorlog`.

#### Exemplo de Configuração de Logging

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily', 'slack'],
    ],

    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],

    'slack' => [
        'driver' => 'slack',
        'url' => env('LOG_SLACK_WEBHOOK_URL'),
        'username' => 'Laravel Log',
        'emoji' => ':boom:',
        'level' => 'critical',
    ],
    // Outros canais...
],
```

### Passo 2: Configurar o Arquivo .env

No arquivo `.env`, defina as variáveis necessárias para o logging, como a URL do webhook do Slack:

```env
LOG_SLACK_WEBHOOK_URL=https://hooks.slack.com/services/your/webhook/url
```

## Logging de Eventos

### Passo 1: Criar um Evento

Crie um evento usando o comando Artisan:

```bash
php artisan make:event MonitoredEvent
```

### Passo 2: Implementar o Evento

Defina o evento em `app/Events/MonitoredEvent.php`:

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;

class MonitoredEvent
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }
}
```

### Passo 3: Criar um Listener

Crie um listener para o evento usando o comando Artisan:

```bash
php artisan make:listener LogMonitoredEvent
```

### Passo 4: Implementar o Listener com Logging

Defina o listener em `app/Listeners/LogMonitoredEvent.php`:

```php
namespace App\Listeners;

use App\Events\MonitoredEvent;
use Illuminate\Support\Facades\Log;

class LogMonitoredEvent
{
    public function handle(MonitoredEvent $event)
    {
        Log::info('MonitoredEvent disparado', ['data' => $event->data]);
    }
}
```

### Passo 5: Registrar o Listener

Registre o listener no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\MonitoredEvent' => [
        'App\Listeners\LogMonitoredEvent',
    ],
];
```

## Ferramentas de Monitoramento

### Passo 1: Configurar o Laravel Horizon

O Laravel Horizon oferece uma interface gráfica para monitorar filas e jobs. Instale o Horizon:

```bash
composer require laravel/horizon
```

### Passo 2: Configurar o Horizon

Publique a configuração do Horizon:

```bash
php artisan horizon:install
```

Configure o Horizon no arquivo `config/horizon.php`:

```php
'environments' => [
    'production' => [
        'supervisor-1' => [
            'connection' => 'redis',
            'queue' => ['default'],
            'balance' => 'auto',
            'processes' => 10,
            'tries' => 3,
        ],
    ],
],
```

### Passo 3: Iniciar o Horizon

Inicie o Horizon com o comando Artisan:

```bash
php artisan horizon
```

### Passo 4: Acessar o Dashboard do Horizon

Acesse o dashboard do Horizon em sua aplicação Laravel na URL `/horizon`.

## Resumo

Monitorar e registrar eventos no Laravel é essencial para garantir a integridade dos processos e a detecção precoce de problemas. Com a configuração adequada e o uso de ferramentas como Laravel Horizon, você pode manter uma visão detalhada das atividades do sistema, facilitando a depuração, auditoria e análise de desempenho.
