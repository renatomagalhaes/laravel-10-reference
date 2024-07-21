# Laravel Horizon

Laravel Horizon é uma ferramenta poderosa para gerenciar filas e jobs no Laravel. Ele fornece uma interface gráfica para monitorar, configurar e gerenciar seus workers e filas, permitindo uma visão detalhada do desempenho e status dos jobs. Este guia aborda como configurar e usar o Laravel Horizon.

## Introdução ao Horizon

### O que é o Laravel Horizon?

Laravel Horizon é um painel de controle para gerenciar filas Redis no Laravel. Ele permite monitorar jobs, filas, e workers em tempo real, além de fornecer estatísticas detalhadas sobre o desempenho dos jobs.

### Instalação do Horizon

Para instalar o Laravel Horizon, use o Composer:

```bash
composer require laravel/horizon
```

### Publicar os Assets do Horizon

Após a instalação, publique os assets do Horizon usando o comando Artisan:

```bash
php artisan horizon:install
```

## Configuração do Horizon

### Configurar o Arquivo horizon.php

O arquivo de configuração `config/horizon.php` permite definir diversas opções para o funcionamento do Horizon, incluindo a configuração dos processos de supervisão, filas, e workers.

Exemplo de configuração básica:

```php
return [
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
];
```

### Executar o Horizon

Para iniciar o Horizon, use o comando Artisan:

```bash
php artisan horizon
```

O Horizon estará disponível em sua aplicação Laravel na URL `/horizon`.

## Monitoramento de Filas e Jobs

### Interface do Horizon

A interface do Horizon permite visualizar o status das filas e jobs em tempo real, incluindo:

- Status dos jobs (pendentes, em execução, falhados, concluídos).
- Estatísticas de jobs (tempo médio de execução, taxa de falhas).
- Histórico detalhado dos jobs.

### Alertas e Notificações

O Horizon permite configurar alertas para diferentes eventos, como quando um job falha ou uma fila acumula um número excessivo de jobs pendentes. Configure os alertas no arquivo `config/horizon.php`:

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

## Manutenção e Otimização

### Balanceamento de Carga

O Horizon oferece várias estratégias de balanceamento de carga para distribuir os jobs entre os workers, como `simple`, `auto`, e `redis`.

### Supervisores

Supervisores são responsáveis por gerenciar os processos dos workers. Configure supervisores no arquivo `horizon.php` para ajustar o número de processos e definir a estratégia de balanceamento:

```php
'supervisor-1' => [
    'connection' => 'redis',
    'queue' => ['default'],
    'balance' => 'auto',
    'processes' => 10,
    'tries' => 3,
],
```

## Segurança

### Acesso ao Horizon

Para proteger o acesso ao Horizon, você pode configurar uma política de autorização no arquivo `app/Providers/HorizonServiceProvider.php`:

```php
use Laravel\Horizon\Horizon;

Horizon::auth(function ($request) {
    return auth()->check() && auth()->user()->is_admin;
});
```

## Resumo

O Laravel Horizon é uma ferramenta essencial para gerenciar e monitorar filas e jobs no Laravel. Com uma interface gráfica intuitiva e recursos avançados de monitoramento e configuração, o Horizon facilita a administração de tarefas assíncronas, melhorando a eficiência e a confiabilidade da sua aplicação.
