# Trabalhando com Kafka

Apache Kafka é uma plataforma de streaming distribuída que permite a publicação e a assinatura de streams de registros em tempo real. Este guia aborda como configurar e usar Kafka com o Laravel para gerenciar filas e jobs.

## Configuração Inicial

### Passo 1: Instalar o Package do Laravel para Kafka

Para usar Kafka no Laravel, primeiro instale um pacote que suporte Kafka, como o `laravel-queue-kafka`:

```bash
composer require joblocal/laravel-queue-kafka
```

### Passo 2: Configurar o Arquivo queue.php

No arquivo `config/queue.php`, adicione a configuração para Kafka:

```php
'connections' => [
    'kafka' => [
        'driver' => 'kafka',
        'queue' => env('KAFKA_QUEUE', 'default'),
        'topics' => [
            [
                'topic' => env('KAFKA_TOPIC', 'default'),
                'partitions' => env('KAFKA_PARTITIONS', 1),
                'replication_factor' => env('KAFKA_REPLICATION_FACTOR', 1),
            ],
        ],
        'brokers' => env('KAFKA_BROKERS', '127.0.0.1:9092'),
    ],
],
```

### Passo 3: Configurar Credenciais do Kafka

Adicione as credenciais do Kafka ao arquivo `.env`:

```env
KAFKA_BROKERS=127.0.0.1:9092
KAFKA_QUEUE=default
KAFKA_TOPIC=default
KAFKA_PARTITIONS=1
KAFKA_REPLICATION_FACTOR=1
```

## Criando e Despachando Jobs para Kafka

### Passo 1: Criar um Job

Use o comando Artisan para criar um job:

```bash
php artisan make:job ExampleKafkaJob
```

### Passo 2: Definir a Lógica do Job

No arquivo `app/Jobs/ExampleKafkaJob.php`, defina a lógica do job:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class ExampleKafkaJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function handle()
    {
        // Lógica do job
        \Log::info('Job processado com sucesso: ' . $this->data);
    }
}
```

### Passo 3: Despachar o Job para Kafka

No seu controlador, despache o job para a fila Kafka:

```php
use App\Jobs\ExampleKafkaJob;

class ExampleController extends Controller
{
    public function dispatchToKafka()
    {
        $data = 'Este é um exemplo de dado';
        ExampleKafkaJob::dispatch($data)->onConnection('kafka');
        return response()->json(['message' => 'Job despachado para o Kafka com sucesso!']);
    }
}
```

## Monitoramento e Gestão de Filas Kafka

### Verificar Filas e Jobs

Use o comando Artisan para verificar o status das filas e jobs:

```bash
php artisan queue:work kafka
```

### Configuração de Workers

Os workers são responsáveis por processar os jobs enfileirados no Kafka. Configure os workers no arquivo `supervisor.conf` para gerenciamento avançado com o Supervisor:

```ini
[program:laravel-kafka-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/your/app/artisan queue:work kafka --sleep=3 --tries=3
autostart=true
autorestart=true
user=your-username
numprocs=8
redirect_stderr=true
stdout_logfile=/path/to/your/app/worker.log
```

## Segurança e Boas Práticas

### Configuração de Políticas de Segurança

Certifique-se de configurar as políticas de segurança do Kafka para controlar o acesso à fila. Use as políticas de segurança do Kafka para definir controles de acesso detalhados.

### Monitoramento e Alertas

Utilize ferramentas de monitoramento como o Kafka Manager para monitorar a performance das filas e configurar alertas para eventos importantes, como falhas de jobs.

### Boas Práticas

- **Retentativas e Fallbacks**: Configure retentativas automáticas para jobs que falham temporariamente e defina lógica de fallback.
- **Dead Letter Topics (DLT)**: Configure DLT para gerenciar jobs que falharam permanentemente.

## Resumo

Kafka é uma solução robusta para gerenciar filas e jobs em aplicações Laravel. Com a configuração correta e o uso de boas práticas de segurança e monitoramento, você pode escalar suas filas de maneira eficiente e garantir a entrega confiável de mensagens. Utilizando o Kafka, você pode otimizar ainda mais o processamento de jobs na sua aplicação.
