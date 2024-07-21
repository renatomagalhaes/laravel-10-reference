# Monitoramento e Alertas de Filas

Monitorar filas e implementar alertas é essencial para garantir que jobs sejam processados eficientemente e que qualquer problema seja detectado e resolvido rapidamente. Este guia aborda como configurar monitoramento e alertas para filas no Laravel.

## Ferramentas de Monitoramento

### 1. Laravel Horizon

Laravel Horizon fornece uma interface gráfica para monitorar e gerenciar suas filas Redis. Ele oferece informações em tempo real sobre jobs, filas e workers.

#### Instalação e Configuração do Horizon

Para instalar o Horizon, use o Composer:

```bash
composer require laravel/horizon
```

Publique os assets do Horizon:

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
    'local' => [
        'supervisor-1' => [
            'connection' => 'redis',
            'queue' => ['default'],
            'balance' => 'simple',
            'processes' => 3,
            'tries' => 3,
        ],
    ],
],
```

Inicie o Horizon:

```bash
php artisan horizon
```

Acesse o Horizon em sua aplicação Laravel na URL `/horizon`.

### 2. New Relic

New Relic é uma ferramenta de monitoramento de performance que pode ser integrada ao Laravel para monitorar filas e jobs.

#### Integração com New Relic

Instale a extensão New Relic para PHP e configure-a conforme a documentação oficial da New Relic.

### 3. Datadog

Datadog é outra ferramenta de monitoramento que pode ser integrada ao Laravel para obter insights detalhados sobre o desempenho das filas.

#### Integração com Datadog

Siga a documentação oficial do Datadog para integrar com o Laravel, configurando a coleta de métricas e logs das filas.

## Configuração de Alertas

### 1. Alertas no Horizon

Laravel Horizon permite configurar alertas para diferentes eventos, como quando um job falha ou uma fila acumula um número excessivo de jobs pendentes.

#### Exemplo de Configuração de Alertas no Horizon

No arquivo `config/horizon.php`, configure as notificações:

```php
'waits' => [
    'redis:default' => 60,
],

'notifications' => [
    'slack' => [
        'webhook_url' => env('HORIZON_SLACK_WEBHOOK_URL'),
        'channel' => '#horizon',
        'username' => 'Horizon',
        'emoji' => ':boom:',
    ],
],
```

### 2. Alertas no New Relic

Configure alertas no New Relic para monitorar métricas específicas das filas e receber notificações por email ou outros canais quando algo sair do esperado.

### 3. Alertas no Datadog

Configure monitoramentos e alertas no Datadog para filas e jobs, definindo métricas e condições que disparem alertas.

## Boas Práticas de Monitoramento

### Coleta de Métricas

Colete métricas detalhadas sobre o desempenho das filas, como tempo médio de processamento, taxa de falhas e número de jobs pendentes.

### Logs Detalhados

Mantenha logs detalhados de todos os jobs processados, incluindo informações sobre falhas e retentativas.

### Automação de Retentativas

Implemente lógica automatizada para retentar jobs que falham temporariamente, reduzindo a necessidade de intervenção manual.

### Revisão Regular

Revise regularmente as métricas e logs das filas para identificar padrões e áreas que precisam de otimização.

## Resumo

Monitorar e configurar alertas para filas no Laravel é essencial para garantir o processamento eficiente e a resolução rápida de problemas. Ferramentas como Laravel Horizon, New Relic e Datadog oferecem funcionalidades avançadas para monitoramento e alertas, ajudando a manter suas filas e jobs sob controle.
