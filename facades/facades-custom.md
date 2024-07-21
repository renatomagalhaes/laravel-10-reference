# Custom Facades

Facades personalizadas permitem encapsular lógica complexa em uma interface simples e reutilizável. Isso pode ajudar a organizar seu código e tornar a aplicação mais modular.

## Criando uma Facade Personalizada

Para criar uma facade personalizada, você precisa criar uma classe de facade e um service provider para registrar a facade no container de serviços do Laravel.

### Exemplo de Facade Personalizada

#### Passo 1: Criar a Classe de Facade

```php
namespace App\Facades;

use Illuminate\Support\Facades\Facade;

class CustomService extends Facade
{
    protected static function getFacadeAccessor()
    {
        return 'customservice';
    }
}
```

#### Passo 2: Criar o Serviço

```php
namespace App\Services;

class CustomService
{
    public function performTask()
    {
        return 'Task performed!';
    }
}
```

#### Passo 3: Registrar o Serviço

```php
namespace App\Providers;

use Illuminate\Support\ServiceProvider;
use App\Services\CustomService;

class CustomServiceProvider extends ServiceProvider
{
    public function register()
    {
        $this->app->singleton('customservice', function ($app) {
            return new CustomService();
        });
    }

    public function boot()
    {
        //
    }
}
```

#### Passo 4: Adicionar o Service Provider

Adicione o `CustomServiceProvider` ao array de providers em `config/app.php`.

```php
'providers' => [
    // Other Service Providers

    App\Providers\CustomServiceProvider::class,
];
```

#### Usando a Facade Personalizada

```php
use App\Facades\CustomService;

$result = CustomService::performTask();
echo $result; // Output: Task performed!
```

## Resumo

Facades personalizadas são uma ferramenta poderosa para encapsular lógica complexa e tornar sua aplicação mais modular e organizada. Seguindo os passos acima, você pode criar e utilizar facades personalizadas em sua aplicação Laravel.
