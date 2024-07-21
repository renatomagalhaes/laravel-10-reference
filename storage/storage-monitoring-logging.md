# Monitoramento e Log de Operações de Arquivo

O monitoramento e o log de operações de arquivo são fundamentais para rastrear atividades, detectar anomalias e garantir a segurança e a integridade dos dados armazenados. O Laravel facilita a implementação de monitoramento e log através de seu sistema de logging integrado e eventos personalizados.

## Passo 1: Configurar o Sistema de Logging

O Laravel usa o Monolog como biblioteca de logging, permitindo a configuração de diferentes canais de log no arquivo `config/logging.php`.

### Exemplo de Configuração de Canal de Log Personalizado

```php
'channels' => [
    'stack' => [
        'driver' => 'stack',
        'channels' => ['daily'],
    ],

    'daily' => [
        'driver' => 'daily',
        'path' => storage_path('logs/laravel.log'),
        'level' => 'debug',
        'days' => 14,
    ],

    'file_operations' => [
        'driver' => 'single',
        'path' => storage_path('logs/file_operations.log'),
        'level' => 'info',
    ],
],
```

## Passo 2: Logar Operações de Arquivo

Você pode logar operações de arquivo utilizando o facade `Log` para registrar atividades importantes.

### Exemplo de Log de Upload de Arquivo

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;
use Illuminate\Support\Facades\Storage;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240',
    ]);

    $path = $request->file('file')->store('uploads');

    // Log da operação de upload
    Log::channel('file_operations')->info('Arquivo enviado', ['path' => $path, 'user_id' => auth()->id()]);

    return response()->json(['path' => $path]);
});
```

### Exemplo de Log de Exclusão de Arquivo

```php
Route::delete('/delete/{file}', function ($file) {
    if (Storage::exists('uploads/' . $file)) {
        Storage::delete('uploads/' . $file);
        // Log da operação de exclusão
        Log::channel('file_operations')->info('Arquivo excluído', ['file' => $file, 'user_id' => auth()->id()]);
        return response()->json(['message' => 'Arquivo excluído com sucesso.']);
    }
    return response()->json(['message' => 'Arquivo não encontrado.'], 404);
});
```

## Passo 3: Monitoramento em Tempo Real

Para monitoramento em tempo real, você pode integrar serviços como Sentry, New Relic ou ELK Stack (Elasticsearch, Logstash, Kibana).

### Exemplo de Integração com Sentry

Instale o pacote Sentry:

```bash
composer require sentry/sentry-laravel
```

Configure o Sentry no arquivo `.env`:

```env
SENTRY_LARAVEL_DSN=https://examplePublicKey@o0.ingest.sentry.io/0
```

Adicione a configuração no arquivo `config/logging.php`:

```php
'channels' => [
    'sentry' => [
        'driver' => 'sentry',
        'level' => 'error',
    ],
],
```

### Exemplo de Log de Erros com Sentry

```php
use Illuminate\Support\Facades\Log;

try {
    // Código que pode lançar exceções
} catch (\Exception $e) {
    Log::channel('sentry')->error('Erro capturado', ['exception' => $e]);
}
```

## Passo 4: Criar Eventos e Listeners para Monitoramento

Além de logar diretamente nas rotas, você pode criar eventos e listeners para centralizar o monitoramento de operações de arquivo.

### Exemplo de Evento Personalizado

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;

class FileOperation
{
    use Dispatchable, SerializesModels;

    public $operation;
    public $filePath;

    public function __construct($operation, $filePath)
    {
        $this->operation = $operation;
        $this->filePath = $filePath;
    }
}
```

### Exemplo de Listener Personalizado

```php
namespace App\Listeners;

use App\Events\FileOperation;
use Illuminate\Support\Facades\Log;

class LogFileOperation
{
    public function handle(FileOperation $event)
    {
        Log::channel('file_operations')->info('Operação de arquivo', [
            'operation' => $event->operation,
            'path' => $event->filePath,
            'user_id' => auth()->id(),
        ]);
    }
}
```

### Registro do Evento e Listener

No arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    FileOperation::class => [
        LogFileOperation::class,
    ],
];
```

### Disparo do Evento

```php
use App\Events\FileOperation;

Storage::put('path/to/file.txt', 'Conteúdo do arquivo');
event(new FileOperation('upload', 'path/to/file.txt'));
```

## Resumo

O monitoramento e o log de operações de arquivo no Laravel são fundamentais para garantir a segurança e a integridade dos dados armazenados. Utilizando o sistema de logging integrado e eventos personalizados, você pode rastrear atividades, detectar anomalias e responder rapidamente a problemas, proporcionando uma aplicação mais segura e confiável.
