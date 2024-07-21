# Comandos Artisan em Cenários Avançados

Os comandos Artisan podem ser usados em cenários avançados para atender a requisitos específicos e complexos da aplicação. Este guia aborda alguns desses cenários e como implementar práticas avançadas com comandos Artisan no Laravel.

## Introdução aos Cenários Avançados

### O que são Cenários Avançados?

Cenários avançados envolvem a utilização de comandos Artisan para resolver problemas complexos, integrar com outros sistemas, e executar operações críticas que vão além do uso básico dos comandos.

### Benefícios dos Cenários Avançados

- **Flexibilidade:** Permite adaptar os comandos às necessidades específicas da aplicação.
- **Escalabilidade:** Facilita a gestão de operações complexas em larga escala.
- **Automação:** Aumenta a eficiência através da automação de tarefas complexas.

## Comandos Interativos Avançados

### Criação de Comandos Interativos

Comandos interativos avançados podem solicitar várias entradas do usuário e executar operações com base nessas respostas.

#### Exemplo de Comando Interativo Avançado

```php
namespace App\Console\Commands;

use Illuminate\Console\Command;

class AdvancedInteractiveCommand extends Command
{
    protected $signature = 'command:advanced-interactive';
    protected $description = 'Comando interativo avançado de exemplo';

    public function handle()
    {
        $name = $this->ask('Qual é o seu nome?');
        $email = $this->ask('Qual é o seu email?');
        $password = $this->secret('Digite uma senha');

        if ($this->confirm('Você deseja criar um usuário com essas informações?')) {
            // Lógica para criar o usuário
            $this->info("Usuário {$name} criado com sucesso!");
        } else {
            $this->info('Operação cancelada.');
        }
    }
}
```

## Uso de Tabelas e Barras de Progresso em Operações Complexas

### Exibindo Dados Complexos com Tabelas

Tabelas podem ser usadas para exibir dados complexos de forma organizada, facilitando a análise.

#### Exemplo de Tabela com Dados Relacionais

```php
public function handle()
{
    $headers = ['ID', 'Nome', 'Email', 'Pedidos'];
    $users = \App\Models\User::with('orders')->get()->map(function ($user) {
        return [
            'ID' => $user->id,
            'Nome' => $user->name,
            'Email' => $user->email,
            'Pedidos' => $user->orders->count(),
        ];
    })->toArray();

    $this->table($headers, $users);
}
```

### Usando Barras de Progresso em Processos Longos

Barras de progresso podem ser utilizadas em processos longos para fornecer feedback visual ao usuário.

#### Exemplo de Barra de Progresso em Processamento em Lote

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

## Integração com Sistemas Externos

### Comunicação com APIs Externas

Use comandos Artisan para integrar e comunicar com APIs externas, automatizando a troca de dados.

#### Exemplo de Integração com API Externa

```php
public function handle()
{
    $response = Http::get('https://api.example.com/data');
    
    if ($response->successful()) {
        $this->info('Dados obtidos com sucesso!');
        // Processar os dados
    } else {
        $this->error('Falha ao obter dados da API.');
    }
}
```

### Processamento de Arquivos

Comandos Artisan podem ser usados para processar arquivos, como a importação e exportação de dados em massa.

#### Exemplo de Importação de Dados a Partir de um Arquivo CSV

```php
public function handle()
{
    $path = storage_path('app/data.csv');
    $data = array_map('str_getcsv', file($path));

    foreach ($data as $row) {
        // Processar cada linha do CSV
        \App\Models\Record::create([
            'field1' => $row[0],
            'field2' => $row[1],
            // ...
        ]);
    }

    $this->info('Dados importados com sucesso!');
}
```

## Gerenciamento de Logs e Auditoria

### Registro Detalhado de Execuções

Implemente logs detalhados para monitorar a execução de comandos, facilitando a auditoria e o diagnóstico de problemas.

#### Exemplo de Registro de Logs Detalhados

```php
use Illuminate\Support\Facades\Log;

public function handle()
{
    Log::info('Início da execução do comando');
    
    try {
        // Lógica do comando
        $this->info('Comando executado com sucesso');
    } catch (\Exception $e) {
        Log::error('Erro ao executar o comando: ' . $e->getMessage());
        $this->error('Erro ao executar o comando');
    }
    
    Log::info('Fim da execução do comando');
}
```

## Resumo

Comandos Artisan em cenários avançados permitem resolver problemas complexos e integrar a aplicação com outros sistemas de maneira eficiente. Utilizando técnicas como criação de comandos interativos, exibição de dados com tabelas e barras de progresso, comunicação com APIs externas, processamento de arquivos e gerenciamento detalhado de logs, você pode aprimorar significativamente a funcionalidade e a eficiência da sua aplicação Laravel.
