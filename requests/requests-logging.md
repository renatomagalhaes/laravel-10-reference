# Logging de Requisições

O logging de requisições é essencial para monitorar e depurar o comportamento de uma aplicação. O Laravel facilita a implementação de logging detalhado para capturar informações sobre as requisições, ajudando a identificar e resolver problemas rapidamente.

## Configuração de Logging

### Passo 1: Configurar o Log Channel

No arquivo `config/logging.php`, você pode configurar diferentes canais de log. Por exemplo, você pode criar um canal personalizado para logs de requisições.

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['single'],
    ],

    'requests' => [
        'driver' => 'daily',
        'path' => storage_path('logs/requests.log'),
        'level' => 'info',
        'days' => 14,
    ],
    // Outros canais de log...
],
```

## Middleware de Logging

### Passo 1: Criar Middleware de Logging

Crie um middleware para registrar as requisições.

```bash
php artisan make:middleware LogRequests
```

### Passo 2: Implementar a Lógica de Logging

No middleware, implemente a lógica para capturar e registrar as informações das requisições.

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class LogRequests
{
    public function handle(Request $request, Closure $next)
    {
        $response = $next($request);

        Log::channel('requests')->info('Request Logged', [
            'method' => $request->method(),
            'url' => $request->fullUrl(),
            'ip' => $request->ip(),
            'user_agent' => $request->userAgent(),
            'status' => $response->status(),
        ]);

        return $response;
    }
}
```

### Passo 3: Registrar Middleware Globalmente

Registre o middleware globalmente no arquivo `app/Http/Kernel.php`.

```php
protected $middleware = [
    // Outros middlewares...
    \App\Http\Middleware\LogRequests::class,
];
```

## Logging de Erros e Exceções

### Passo 1: Configurar Logging de Exceções

O Laravel registra automaticamente as exceções no log padrão. Você pode configurar os detalhes desses logs no arquivo `config/logging.php`.

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily', 'slack'],
        'ignore_exceptions' => false,
    ],
    // Outros canais de log...
],
```

### Passo 2: Capturar e Registrar Erros Personalizados

Você pode capturar e registrar erros personalizados em seus controladores ou serviços.

```php
try {
    // Código que pode lançar exceção...
} catch (\Exception $e) {
    Log::error('Erro ao processar requisição', ['exception' => $e]);
}
```

## Análise de Logs

### Passo 1: Ferramentas de Análise de Logs

Utilize ferramentas de análise de logs para monitorar e analisar os logs da sua aplicação. Ferramentas como ELK Stack (Elasticsearch, Logstash, Kibana) ou serviços de log como Sentry e Loggly podem ser integrados ao Laravel.

### Exemplo de Integração com ELK Stack

```php
'channels' => [
    'elk' => [
        'driver' => 'custom',
        'via' => App\Logging\CreateElkLogger::class,
    ],
    // Outros canais de log...
],
```

## Resumo

O logging de requisições no Laravel é uma prática essencial para monitorar e depurar o comportamento da aplicação. Utilizando middlewares personalizados, configurando canais de log específicos e integrando ferramentas de análise de logs, você pode obter uma visão detalhada das operações da sua aplicação e identificar rapidamente quaisquer problemas ou anomalias.
