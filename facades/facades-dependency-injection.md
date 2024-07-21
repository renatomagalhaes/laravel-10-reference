# Facades e Dependency Injection

Combinar facades com injeção de dependência permite manter a flexibilidade e testabilidade do seu código, enquanto ainda se beneficia da simplicidade das facades.

## Usando Facades com Dependency Injection

Você pode utilizar facades junto com a injeção de dependência para obter o melhor dos dois mundos.

### Exemplo de Uso

#### Injeção de Dependência

```php
namespace App\Http\Controllers;

use App\Services\CustomService;

class CustomController extends Controller
{
    protected $customService;

    public function __construct(CustomService $customService)
    {
        $this->customService = $customService;
    }

    public function index()
    {
        return $this->customService->performTask();
    }
}
```

#### Facades

```php
use App\Facades\CustomService;

$result = CustomService::performTask();
echo $result; // Output: Task performed!
```

## Resumo

Combinar facades com injeção de dependência permite criar código flexível e testável, mantendo a simplicidade e organização proporcionadas pelas facades. Utilize a injeção de dependência para serviços que requerem testabilidade e flexibilidade, e facades para simplificar o acesso a esses serviços.
