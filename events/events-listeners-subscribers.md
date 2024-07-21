# Listeners e Subscribers

Listeners e subscribers são componentes essenciais para a arquitetura baseada em eventos no Laravel. Eles permitem que diferentes partes da aplicação respondam a eventos específicos. Este guia aborda como criar e gerenciar listeners e subscribers.

## Criando Listeners

### Passo 1: Criar um Listener

Para criar um listener, use o comando Artisan:

```bash
php artisan make:listener ExampleListener
```

Isso criará um arquivo de listener em `app/Listeners/ExampleListener.php` com a estrutura básica:

```php
namespace App\Listeners;

use App\Events\ExampleEvent;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class ExampleListener implements ShouldQueue
{
    use InteractsWithQueue;

    public function handle(ExampleEvent $event)
    {
        // Lógica do listener
    }
}
```

### Passo 2: Implementar a Lógica do Listener

Adicione a lógica necessária no método `handle` do listener:

```php
public function handle(ExampleEvent $event)
{
    // Lógica para processar o evento
}
```

## Registrando Listeners

### Passo 1: Registrar no EventServiceProvider

Registre os listeners no arquivo `EventServiceProvider.php`:

```php
protected $listen = [
    'App\Events\ExampleEvent' => [
        'App\Listeners\ExampleListener',
    ],
];
```

### Passo 2: Atualizar o EventServiceProvider

Certifique-se de que o `EventServiceProvider` esteja registrado no arquivo `config/app.php`:

```php
'providers' => [
    // Outros provedores de serviço
    App\Providers\EventServiceProvider::class,
];
```

## Criando Subscribers

### Passo 1: Criar um Subscriber

Subscribers permitem que você agrupe vários listeners relacionados dentro de uma única classe. Para criar um subscriber, use o comando Artisan:

```bash
php artisan make:listener ExampleSubscriber --event-subscriber
```

Isso criará um arquivo de subscriber em `app/Listeners/ExampleSubscriber.php` com a estrutura básica:

```php
namespace App\Listeners;

use Illuminate\Events\Dispatcher;

class ExampleSubscriber
{
    public function subscribe(Dispatcher $events)
    {
        $events->listen(
            'App\Events\ExampleEvent',
            'App\Listeners\ExampleSubscriber@handleExampleEvent'
        );
    }

    public function handleExampleEvent($event)
    {
        // Lógica para processar o evento
    }
}
```

### Passo 2: Registrar o Subscriber

Registre o subscriber no arquivo `EventServiceProvider.php`:

```php
protected $subscribe = [
    'App\Listeners\ExampleSubscriber',
];
```

### Passo 3: Atualizar o EventServiceProvider

Certifique-se de que o `EventServiceProvider` esteja registrado no arquivo `config/app.php`:

```php
'providers' => [
    // Outros provedores de serviço
    App\Providers\EventServiceProvider::class,
];
```

## Enfileirando Listeners

Listeners que implementam a interface `ShouldQueue` serão automaticamente enfileirados pelo Laravel. Isso permite que o processamento de eventos ocorra de forma assíncrona.

### Exemplo de Listener Enfileirado

No arquivo `app/Listeners/ExampleListener.php`:

```php
namespace App\Listeners;

use App\Events\ExampleEvent;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;

class ExampleListener implements ShouldQueue
{
    use InteractsWithQueue;

    public function handle(ExampleEvent $event)
    {
        // Lógica do listener
    }
}
```

## Resumo

Listeners e subscribers no Laravel permitem uma arquitetura baseada em eventos altamente flexível e desacoplada. Com a capacidade de enfileirar listeners e agrupar listeners relacionados em subscribers, você pode organizar e gerenciar eficientemente a lógica de resposta a eventos na sua aplicação.
