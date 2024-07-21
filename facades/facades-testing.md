# Facades e Testes Unitários

Testar facades pode ser desafiador, mas utilizando mocks e espiões, você pode garantir que seu código funciona corretamente.

## Testando Facades com Mocks

Você pode usar o método `shouldReceive` para criar mocks de facades durante os testes.

### Exemplo de Teste com Mocks

#### Facade

```php
use App\Facades\CustomService;

CustomService::shouldReceive('performTask')
    ->once()
    ->andReturn('Mocked Task performed!');
```

#### Teste Unitário

```php
namespace Tests\Unit;

use Tests\TestCase;
use App\Facades\CustomService;

class CustomServiceTest extends TestCase
{
    public function testPerformTask()
    {
        CustomService::shouldReceive('performTask')
            ->once()
            ->andReturn('Mocked Task performed!');

        $this->assertEquals('Mocked Task performed!', CustomService::performTask());
    }
}
```

## Resumo

Testar facades utilizando mocks e espiões garante que seu código funcione corretamente e que as interações com os serviços subjacentes sejam simuladas de maneira precisa. Utilize esses métodos para criar testes robustos e confiáveis.
