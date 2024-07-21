# Requisições Assíncronas

Requisições assíncronas permitem que você execute operações HTTP sem bloquear o fluxo principal do programa. No Laravel, você pode fazer uso de bibliotecas como Guzzle ou até mesmo as ferramentas nativas do PHP para realizar essas requisições.

## Uso de Guzzle para Requisições Assíncronas

### Passo 1: Instalar Guzzle

Se Guzzle não estiver instalado em sua aplicação Laravel, você pode instalá-lo usando Composer:

```bash
composer require guzzlehttp/guzzle
```

### Passo 2: Fazer Requisições Assíncronas com Guzzle

Você pode usar o cliente HTTP do Laravel, que é uma interface sobre o Guzzle, para fazer requisições assíncronas.

#### Exemplo de Requisição GET Assíncrona

```php
use Illuminate\Support\Facades\Http;

$promise = Http::async()->get('https://api.example.com/users')->then(function ($response) {
    $users = $response->json();
    // Processar os dados...
});

// Execute outras tarefas enquanto a requisição é processada

$promise->wait(); // Espera até que a requisição seja concluída
```

### Passo 3: Fazer Requisições POST Assíncronas

Você também pode enviar dados para uma API externa utilizando requisições POST assíncronas.

#### Exemplo de Requisição POST Assíncrona

```php
$promise = Http::async()->post('https://api.example.com/users', [
    'name' => 'John Doe',
    'email' => 'john@example.com',
])->then(function ($response) {
    $user = $response->json();
    // Processar os dados...
});

// Execute outras tarefas enquanto a requisição é processada

$promise->wait(); // Espera até que a requisição seja concluída
```

## Uso de Promessas em PHP

### Passo 1: Instalar a Biblioteca ReactPHP

ReactPHP é uma biblioteca popular para programação assíncrona em PHP. Você pode instalá-la usando Composer:

```bash
composer require react/http
```

### Passo 2: Fazer Requisições Assíncronas com ReactPHP

Com ReactPHP, você pode fazer requisições assíncronas diretamente.

#### Exemplo de Requisição GET Assíncrona

```php
use React\Http\Browser;
use Psr\Http\Message\ResponseInterface;
use React\EventLoop\Factory;

$loop = Factory::create();
$client = new Browser($loop);

$client->get('https://api.example.com/users')->then(
    function (ResponseInterface $response) {
        echo $response->getBody();
    },
    function (Exception $e) {
        echo 'Erro: ' . $e->getMessage();
    }
);

$loop->run();
```

### Passo 3: Fazer Requisições POST Assíncronas com ReactPHP

Você também pode enviar dados com ReactPHP.

#### Exemplo de Requisição POST Assíncrona

```php
$client->post('https://api.example.com/users', [
    'Content-Type' => 'application/json'
], json_encode([
    'name' => 'John Doe',
    'email' => 'john@example.com',
]))->then(
    function (ResponseInterface $response) {
        echo $response->getBody();
    },
    function (Exception $e) {
        echo 'Erro: ' . $e->getMessage();
    }
);

$loop->run();
```

## Uso de Tarefas Assíncronas com Filas

### Passo 1: Configurar Filas no Laravel

O Laravel oferece suporte a filas para executar tarefas demoradas em segundo plano. Para configurar filas, adicione o driver de filas em seu arquivo `.env`.

```env
QUEUE_CONNECTION=database
```

### Passo 2: Criar Jobs

Crie uma classe de job para processar a tarefa em segundo plano.

```bash
php artisan make:job ProcessApiRequest
```

### Exemplo de Job

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Foundation\Bus\Dispatchable;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Http;

class ProcessApiRequest implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function handle()
    {
        $response = Http::get('https://api.example.com/users');
        // Processar os dados...
    }
}
```

### Passo 3: Disparar o Job

Você pode disparar o job de qualquer lugar na sua aplicação.

```php
use App\Jobs\ProcessApiRequest;

ProcessApiRequest::dispatch();
```

## Resumo

Requisições assíncronas são uma poderosa ferramenta para melhorar a eficiência e a capacidade de resposta de suas aplicações. Utilizando bibliotecas como Guzzle e ReactPHP, ou até mesmo o sistema de filas do Laravel, você pode realizar operações HTTP de forma não bloqueante, permitindo que seu aplicativo continue a processar outras tarefas enquanto aguarda respostas de APIs externas.
