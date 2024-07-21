# Prioridade de Filas e Escalonamento

Gerenciar a prioridade das filas e o escalonamento de workers é essencial para otimizar a execução de jobs no Laravel. Este guia aborda como definir prioridades para filas, estratégias de escalonamento e práticas recomendadas.

## Definindo Prioridades para Filas

### Configuração de Prioridades

No Laravel, você pode definir a prioridade das filas diretamente ao despachar os jobs. Filas com maior prioridade serão processadas antes das filas com menor prioridade.

#### Exemplo de Despacho com Prioridade

```php
use App\Jobs\HighPriorityJob;
use App\Jobs\LowPriorityJob;

class ExampleController extends Controller
{
    public function dispatchPriorityJobs()
    {
        // Despachar um job para a fila de alta prioridade
        HighPriorityJob::dispatch()->onQueue('high');

        // Despachar um job para a fila de baixa prioridade
        LowPriorityJob::dispatch()->onQueue('low');

        return response()->json(['message' => 'Jobs despachados com diferentes prioridades!']);
    }
}
```

### Configuração no Supervisor

Para garantir que as filas de alta prioridade sejam processadas primeiro, configure o Supervisor para iniciar processos de workers para cada fila com base na prioridade:

```ini
[program:laravel-worker-high]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/your/app/artisan queue:work redis --queue=high --sleep=3 --tries=3
autostart=true
autorestart=true
user=your-username
numprocs=5
redirect_stderr=true
stdout_logfile=/path/to/your/app/worker-high.log

[program:laravel-worker-low]
process_name=%(program_name)s_%(process_num)02d
command=php /path/to/your/app/artisan queue:work redis --queue=low --sleep=3 --tries=3
autostart=true
autorestart=true
user=your-username
numprocs=2
redirect_stderr=true
stdout_logfile=/path/to/your/app/worker-low.log
```

## Estratégias de Escalonamento

### Balanceamento de Carga

O balanceamento de carga permite distribuir jobs entre vários workers para evitar sobrecarga de qualquer worker específico. Laravel Horizon facilita essa tarefa com estratégias de balanceamento, como `simple`, `auto` e `redis`.

#### Configuração no Horizon

```php
'supervisor-1' => [
    'connection' => 'redis',
    'queue' => ['default', 'high', 'low'],
    'balance' => 'auto',
    'processes' => 10,
    'tries' => 3,
],
```

### Adicionar e Remover Workers Dinamicamente

Adicionar e remover workers dinamicamente ajuda a ajustar a capacidade de processamento com base na carga atual. Você pode usar scripts ou ferramentas de automação para ajustar o número de workers em execução.

#### Exemplo de Script para Ajustar Workers

```bash
#!/bin/bash

# Adicionar mais workers
php /path/to/your/app/artisan queue:work redis --queue=default --sleep=3 --tries=3 &

# Remover workers
pkill -f "artisan queue:work redis"
```

## Monitoramento de Performance

### Usar Laravel Horizon

Laravel Horizon oferece uma interface gráfica para monitorar a performance das filas e workers, permitindo ajustes em tempo real.

### Ferramentas de Monitoramento Externas

Ferramentas como New Relic, Datadog e outras podem ser integradas para monitoramento detalhado e alertas.

## Resumo

Gerenciar a prioridade das filas e o escalonamento dos workers é essencial para garantir a eficiência e a responsividade da sua aplicação. Definindo prioridades adequadas e utilizando estratégias de escalonamento, você pode otimizar a execução de jobs, garantindo que tarefas críticas sejam processadas rapidamente e recursos sejam usados de forma eficiente.
