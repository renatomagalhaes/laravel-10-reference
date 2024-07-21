# Eventos e Listeners para Arquivos

O Laravel permite a criação e utilização de eventos e listeners para monitorar e responder a ações relacionadas a arquivos. Isso é útil para executar ações específicas após o upload, modificação ou exclusão de arquivos, melhorando a modularidade e a manutenção do código.

## Passo 1: Definir Eventos

Você pode definir eventos personalizados para ações relacionadas a arquivos.

### Exemplo de Definição de Evento

Crie um evento utilizando o Artisan.

```bash
php artisan make:event FileUploaded
```

No arquivo do evento gerado (`app/Events/FileUploaded.php`), defina os atributos necessários.

```php
namespace App\Events;

use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Queue\SerializesModels;

class FileUploaded
{
    use Dispatchable, SerializesModels;

    public $filePath;

    public function __construct($filePath)
    {
        $this->filePath = $filePath;
    }
}
```

## Passo 2: Definir Listeners

Crie um listener para o evento utilizando o Artisan.

```bash
php artisan make:listener LogFileUpload
```

No arquivo do listener gerado (`app/Listeners/LogFileUpload.php`), defina a lógica a ser executada quando o evento for disparado.

```php
namespace App\Listeners;

use App\Events\FileUploaded;
use Illuminate\Support\Facades\Log;

class LogFileUpload
{
    public function handle(FileUploaded $event)
    {
        Log::info('Arquivo enviado: ' . $event->filePath);
    }
}
```

## Passo 3: Registrar Eventos e Listeners

Registre o evento e o listener no arquivo `EventServiceProvider.php`.

```php
namespace App\Providers;

use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;
use App\Events\FileUploaded;
use App\Listeners\LogFileUpload;

class EventServiceProvider extends ServiceProvider
{
    protected $listen = [
        FileUploaded::class => [
            LogFileUpload::class,
        ],
    ];

    public function boot()
    {
        parent::boot();
    }
}
```

## Passo 4: Disparar Eventos

Dispare o evento quando um arquivo for enviado ou modificado.

### Exemplo de Disparo de Evento após Upload de Arquivo

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;
use App\Events\FileUploaded;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240',
    ]);

    $path = $request->file('file')->store('uploads');

    // Disparar o evento após o upload
    event(new FileUploaded($path));

    return response()->json(['path' => $path]);
});
```

## Exemplos de Uso de Eventos e Listeners

### Notificação após Upload

Envie uma notificação por e-mail após um arquivo ser enviado.

```php
namespace App\Listeners;

use App\Events\FileUploaded;
use Illuminate\Support\Facades\Mail;
use App\Mail\FileUploadedNotification;

class NotifyUserOfFileUpload
{
    public function handle(FileUploaded $event)
    {
        Mail::to('user@example.com')->send(new FileUploadedNotification($event->filePath));
    }
}
```

### Processamento Assíncrono

Inicie um job para processar o arquivo em background após o upload.

```php
namespace App\Listeners;

use App\Events\FileUploaded;
use App\Jobs\ProcessUploadedFile;

class ProcessFileUpload
{
    public function handle(FileUploaded $event)
    {
        ProcessUploadedFile::dispatch($event->filePath);
    }
}
```

## Vantagens do Uso de Eventos e Listeners

- **Modularidade**: Separa a lógica de resposta a eventos da lógica principal, facilitando a manutenção e a evolução do código.
- **Escalabilidade**: Permite adicionar novas ações em resposta a eventos sem modificar a lógica existente.
- **Assincronicidade**: Facilita o processamento assíncrono de tarefas demoradas, melhorando a performance da aplicação.

## Resumo

O uso de eventos e listeners para arquivos no Laravel permite monitorar e responder a ações relacionadas a arquivos de forma eficiente e modular. Com eventos personalizados, você pode executar ações específicas após o upload, modificação ou exclusão de arquivos, melhorando a organização e a escalabilidade do seu código.
