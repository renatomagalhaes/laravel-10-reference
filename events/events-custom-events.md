# Eventos Customizados

Criar eventos customizados no Laravel permite que você implemente funcionalidades específicas para sua aplicação, permitindo uma maior flexibilidade e modularidade. Este guia aborda como criar, registrar e despachar eventos customizados no Laravel.

## Criando um Evento Customizado

### Passo 1: Criar um Evento

Para criar um evento customizado, use o comando Artisan:

```bash
php artisan make:event CustomEvent
```

Isso criará um arquivo de evento em `app/Events/CustomEvent.php` com a estrutura básica:

```php
namespace App\Events;

use Illuminate\Queue\SerializesModels;
use Illuminate\Foundation\Events\Dispatchable;
use Illuminate\Broadcasting\InteractsWithSockets;

class CustomEvent
{
    use Dispatchable, InteractsWithSockets, SerializesModels;

    public $data;

    public function __construct($data)
    {
        $this->data = $data;
    }
}
```

### Passo 2: Implementar a Lógica do Evento

Adicione quaisquer atributos necessários ao evento no construtor:

```php
public function __construct($data)
{
    $this->data = $data;
}
```

## Criando Listeners para o Evento Customizado

### Passo 1: Criar um Listener

Crie um listener para o evento customizado usando o comando Artisan:

```bash
php artisan make:listener CustomEventListener
```

Isso criará um arquivo de listener em `app/Listeners/CustomEventListener.php` com a estrutura básica:

```php
namespace App\Listeners;

use App\Events\CustomEvent;

class CustomEventListener
{
    public function handle(CustomEvent $event)
    {
        // Lógica do listener
    }
}
```

### Passo 2: Registrar o Listener

Registre o listener no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\CustomEvent' => [
        'App\Listeners\CustomEventListener',
    ],
];
```

### Passo 3: Despachar o Evento

Para despachar o evento customizado, use o método `event`:

```php
use App\Events\CustomEvent;

class CustomEventController extends Controller
{
    public function triggerCustomEvent()
    {
        $data = 'Dados do evento customizado';
        event(new CustomEvent($data));

        return response()->json(['message' => 'Evento customizado disparado com sucesso!']);
    }
}
```

## Implementando Lógica de Negócio nos Listeners

Os listeners para eventos customizados podem implementar qualquer lógica de negócio necessária. Aqui está um exemplo de como você pode fazer isso:

```php
namespace App\Listeners;

use App\Events\CustomEvent;

class CustomEventListener
{
    public function handle(CustomEvent $event)
    {
        // Exemplo de lógica de negócio
        \Log::info('Evento customizado recebido com dados: ' . $event->data);
    }
}
```

## Testando Eventos Customizados

### Passo 1: Criar um Teste para o Evento

Crie um teste para garantir que o evento seja disparado corretamente:

```php
namespace Tests\Feature;

use Tests\TestCase;
use App\Events\CustomEvent;
use Illuminate\Support\Facades\Event;

class CustomEventTest extends TestCase
{
    public function testCustomEventIsDispatched()
    {
        Event::fake();

        $data = 'Dados do teste';
        event(new CustomEvent($data));

        Event::assertDispatched(CustomEvent::class, function ($event) use ($data) {
            return $event->data === $data;
        });
    }
}
```

## Resumo

Criar eventos customizados no Laravel permite que você implemente funcionalidades específicas e modulares na sua aplicação. Com a configuração adequada e os exemplos fornecidos, você pode começar a implementar eventos customizados para melhorar a modularidade e a flexibilidade do seu código.
