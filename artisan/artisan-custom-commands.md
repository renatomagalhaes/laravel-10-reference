# Criando Comandos Customizados

O Artisan permite criar comandos customizados que podem ser usados para automatizar tarefas específicas da sua aplicação. Este guia aborda como criar, configurar e usar comandos customizados no Laravel.

## Introdução aos Comandos Customizados

### Por que Criar Comandos Customizados?

Comandos customizados são úteis para automatizar tarefas repetitivas, executar operações complexas e criar ferramentas internas que facilitam o desenvolvimento e a manutenção da aplicação.

## Criando um Novo Comando Customizado

### Passo 1: Gerar o Comando

Use o comando `make:command` para gerar um novo comando:

```bash
php artisan make:command NomeDoComando
```

Este comando criará um novo arquivo de comando em `app/Console/Commands/NomeDoComando.php`.

### Passo 2: Configurar o Comando

Abra o arquivo gerado e configure as propriedades do comando, como `signature` e `description`:

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class NomeDoComando extends Command
{
    // A assinatura do comando na linha de comando.
    protected $signature = 'comando:personalizado';

    // A descrição do comando.
    protected $description = 'Descrição do comando personalizado';

    // Executa o comando.
    public function handle()
    {
        $this->info('Comando personalizado executado com sucesso!');
    }
}
```

### Passo 3: Registrar o Comando

Registre o comando no arquivo `app/Console/Kernel.php` para que ele seja reconhecido pelo Artisan:

```php
protected $commands = [
    \App\Console\Commands\NomeDoComando::class,
];
```

## Usando Argumentos e Opções

### Adicionando Argumentos

Você pode adicionar argumentos ao comando definindo-os na propriedade `signature`:

```php
protected $signature = 'comando:personalizado {argumento}';
```

Recupere o valor do argumento no método `handle`:

```php
public function handle()
{
    $argumento = $this->argument('argumento');
    $this->info("Argumento recebido: {$argumento}");
}
```

### Adicionando Opções

Adicione opções ao comando definindo-as na propriedade `signature`:

```php
protected $signature = 'comando:personalizado {--opcao}';
```

Recupere o valor da opção no método `handle`:

```php
public function handle()
{
    $opcao = $this->option('opcao');
    $this->info("Opção recebida: " . ($opcao ? 'true' : 'false'));
}
```

## Interações via Prompt

### Perguntando ao Usuário

Use o método `ask` para perguntar ao usuário:

```php
public function handle()
{
    $nome = $this->ask('Qual é o seu nome?');
    $this->info("Olá, {$nome}!");
}
```

### Confirmando Ação

Use o método `confirm` para pedir confirmação ao usuário:

```php
public function handle()
{
    if ($this->confirm('Você deseja continuar?')) {
        $this->info('Continuando...');
    } else {
        $this->info('Ação cancelada.');
    }
}
```

## Usando Tabelas e Barras de Progresso

### Exibindo uma Tabela

Use o método `table` para exibir dados em formato de tabela:

```php
public function handle()
{
    $headers = ['ID', 'Nome'];
    $users = [
        [1, 'Alice'],
        [2, 'Bob'],
    ];

    $this->table($headers, $users);
}
```

### Exibindo uma Barra de Progresso

Use o método `withProgressBar` para exibir uma barra de progresso:

```php
public function handle()
{
    $tasks = ['Tarefa 1', 'Tarefa 2', 'Tarefa 3'];

    $this->withProgressBar($tasks, function ($task) {
        $this->info($task);
    });
}
```

## Resumo

Criar comandos customizados no Artisan permite automatizar tarefas e criar ferramentas internas específicas para sua aplicação. Utilizando argumentos, opções, interações via prompt, tabelas e barras de progresso, você pode desenvolver comandos poderosos e interativos para melhorar o fluxo de trabalho no desenvolvimento e manutenção da sua aplicação Laravel.
