# Agendamento com Artisan

O Artisan permite o agendamento de comandos para execução periódica, utilizando o scheduler integrado do Laravel. Este guia aborda como configurar e usar o agendamento de tarefas no Laravel.

## Introdução ao Agendamento

### O que é o Scheduler?

O scheduler do Laravel fornece uma interface fluente e expressiva para definir cron jobs. Ele permite agendar comandos Artisan, fechamentos (closures), chamadas de métodos e scripts externos para serem executados em intervalos definidos.

### Benefícios do Agendamento

- **Automatização de Tarefas:** Facilita a execução automática de tarefas rotineiras.
- **Manutenção Simplificada:** Centraliza a definição e gestão de cron jobs dentro do próprio código Laravel.
- **Flexibilidade:** Permite definir horários de execução complexos com facilidade.

## Configuração do Scheduler

### Passo 1: Definir Cron Job Único

No servidor, adicione um único cron job ao crontab que executa o scheduler do Laravel a cada minuto:

```bash
* * * * * php /caminho/para/seu/projeto/artisan schedule:run >> /dev/null 2>&1
```

### Passo 2: Definir Tarefas no Scheduler

Defina suas tarefas agendadas no método `schedule` do arquivo `app/Console/Kernel.php`:

```php
protected function schedule(Schedule $schedule)
{
    $schedule->command('emails:send')->daily();
    $schedule->call(function () {
        // Código a ser executado
    })->hourly();
}
```

## Tipos de Agendamentos

### Agendando Comandos Artisan

Você pode agendar comandos Artisan usando o método `command`:

```php
$schedule->command('emails:send')->daily();
```

### Agendando Fechamentos (Closures)

Você pode agendar fechamentos diretamente:

```php
$schedule->call(function () {
    // Código a ser executado
})->hourly();
```

### Agendando Métodos de Classe

Agende métodos de classes existentes:

```php
$schedule->call([new \App\Services\BackupService, 'run'])->weekly();
```

### Agendando Scripts Externos

Agende a execução de scripts externos:

```php
$schedule->exec('node /path/to/script.js')->daily();
```

## Frequência de Agendamento

### Exemplos de Frequência

- **A cada minuto:** `->everyMinute()`
- **A cada hora:** `->hourly()`
- **Diariamente:** `->daily()`
- **Semanalmente:** `->weekly()`
- **Mensalmente:** `->monthly()`

### Usando Expressões Cron

Defina expressões cron personalizadas para maior controle sobre os horários de execução:

```php
$schedule->command('emails:send')->cron('0 0 * * *');
```

## Práticas Avançadas de Agendamento

### Evitando Overlapping

Evite que tarefas sejam executadas simultaneamente usando o método `withoutOverlapping`:

```php
$schedule->command('emails:send')->hourly()->withoutOverlapping();
```

### Condicionais no Agendamento

Execute tarefas com base em condições específicas:

```php
$schedule->command('emails:send')->daily()->when(function () {
    return true; // Condição
});
```

### Definindo Limites de Tempo

Defina um limite de tempo máximo para a execução de uma tarefa:

```php
$schedule->command('emails:send')->daily()->timeout(120);
```

### Configurando o Ambiente

Defina o ambiente no qual a tarefa deve ser executada:

```php
$schedule->command('emails:send')->daily()->onOneServer()->environments('production');
```

## Execução de Tarefas em Segundo Plano

### Background Tasks

Execute tarefas em segundo plano para evitar bloqueios na aplicação:

```php
$schedule->command('emails:send')->daily()->runInBackground();
```

## Monitoramento e Logging

### Log de Execuções

Registre as execuções de tarefas para monitoramento:

```php
$schedule->command('emails:send')->daily()->sendOutputTo('/path/to/log.txt');
```

### Notificações de Falha

Receba notificações em caso de falha de execução:

```php
$schedule->command('emails:send')->daily()->emailOutputOnFailure('admin@example.com');
```

## Resumo

O agendamento de tarefas com Artisan no Laravel permite automatizar e gerenciar tarefas rotineiras de forma eficiente. Utilizando a interface fluente do scheduler, você pode definir cron jobs complexos, evitar overlapping, executar tarefas em segundo plano e monitorar suas execuções, garantindo a manutenção e operação suave da sua aplicação.
