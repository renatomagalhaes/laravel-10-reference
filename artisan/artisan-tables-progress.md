# Tabela e Barra de Progresso

Os comandos Artisan podem exibir dados de forma organizada usando tabelas e fornecer feedback visual sobre o progresso de operações longas utilizando barras de progresso. Este guia aborda como criar e usar essas funcionalidades em comandos Artisan no Laravel.

## Exibindo Dados com Tabelas

### Introdução às Tabelas

As tabelas permitem exibir dados tabulares de forma organizada e fácil de entender. O Artisan fornece métodos para criar tabelas diretamente a partir dos comandos.

### Criando uma Tabela

Use o método `table` para exibir dados em formato de tabela. Você precisa definir os cabeçalhos e as linhas de dados.

#### Exemplo Básico

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class ShowUsersCommand extends Command
{
    protected $signature = 'show:users';
    protected $description = 'Exibe uma tabela com os usuários';

    public function handle()
    {
        $headers = ['ID', 'Nome', 'Email'];
        $users = [
            [1, 'Alice', 'alice@example.com'],
            [2, 'Bob', 'bob@example.com'],
        ];

        $this->table($headers, $users);
    }
}
```

### Tabelas com Dados de Banco de Dados

Você pode preencher a tabela com dados recuperados do banco de dados:

```php
public function handle()
{
    $headers = ['ID', 'Nome', 'Email'];
    $users = \App\Models\User::all(['id', 'name', 'email'])->toArray();

    $this->table($headers, $users);
}
```

## Exibindo Barras de Progresso

### Introdução às Barras de Progresso

As barras de progresso fornecem um feedback visual sobre o progresso de operações longas, permitindo que o usuário saiba que o processo está em andamento e estimando o tempo restante.

### Criando uma Barra de Progresso

Use o método `withProgressBar` para exibir uma barra de progresso. Este método aceita um array de itens e um fechamento (closure) para processar cada item.

#### Exemplo Básico

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class ProcessTasksCommand extends Command
{
    protected $signature = 'process:tasks';
    protected $description = 'Processa uma lista de tarefas com uma barra de progresso';

    public function handle()
    {
        $tasks = ['Tarefa 1', 'Tarefa 2', 'Tarefa 3'];

        $this->withProgressBar($tasks, function ($task) {
            // Simular tempo de processamento
            sleep(1);
            $this->info($task);
        });

        $this->info('Todas as tarefas foram processadas!');
    }
}
```

### Barras de Progresso com Contagem de Iterações

Se você precisar de mais controle, pode criar uma barra de progresso manualmente e avançar conforme necessário:

```php
public function handle()
{
    $tasks = range(1, 10);
    $bar = $this->output->createProgressBar(count($tasks));

    $bar->start();

    foreach ($tasks as $task) {
        // Simular tempo de processamento
        sleep(1);
        $bar->advance();
    }

    $bar->finish();

    $this->info('Todas as tarefas foram processadas!');
}
```

## Exemplos Práticos

### Tabela com Dados de Produtos

Crie um comando que exibe uma tabela com uma lista de produtos:

```php
public function handle()
{
    $headers = ['ID', 'Nome', 'Preço'];
    $products = \App\Models\Product::all(['id', 'name', 'price'])->toArray();

    $this->table($headers, $products);
}
```

### Barra de Progresso para Processamento de Pedidos

Crie um comando que processa pedidos com uma barra de progresso:

```php
public function handle()
{
    $orders = \App\Models\Order::where('status', 'pending')->get();
    $bar = $this->output->createProgressBar($orders->count());

    $bar->start();

    foreach ($orders as $order) {
        // Processar o pedido
        $order->update(['status' => 'processed']);
        $bar->advance();
    }

    $bar->finish();

    $this->info('Todos os pedidos foram processados!');
}
```

## Resumo

Usar tabelas e barras de progresso nos comandos Artisan do Laravel melhora a visualização de dados e fornece feedback útil durante a execução de operações longas. Com as ferramentas fornecidas pelo Artisan, você pode criar comandos interativos e informativos que melhoram a experiência do usuário e facilitam o desenvolvimento e a manutenção da sua aplicação.
