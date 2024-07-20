# Paginação

A paginação é uma técnica importante para lidar com grandes volumes de dados, permitindo dividir os resultados em partes menores e mais manejáveis. O Laravel fornece suporte nativo para paginação através do Eloquent e do Query Builder.

## Paginação Básica

Você pode paginar resultados usando o método `paginate` no Eloquent ou no Query Builder.

### Exemplo de Paginação com Eloquent

```php
namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        $users = User::paginate(15);
        return view('users.index', compact('users'));
    }
}
```

### Exemplo de Paginação com Query Builder

```php
namespace App\Http\Controllers;

use Illuminate\Support\Facades\DB;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        $users = DB::table('users')->paginate(15);
        return view('users.index', compact('users'));
    }
}
```

## Personalizando a Paginação

Você pode personalizar a paginação alterando os parâmetros de exibição e a aparência das páginas.

### Exemplo de Customização de Paginação

```php
$users = User::paginate(15, ['*'], 'page', 2);
```

Neste exemplo, a paginação será iniciada na segunda página.

## Usando o Método `simplePaginate`

O método `simplePaginate` é uma alternativa para a paginação básica que é mais eficiente para grandes conjuntos de dados, pois não realiza uma contagem total dos registros.

### Exemplo de `simplePaginate`

```php
$users = User::simplePaginate(15);
```

## Renderizando Links de Paginação

Para renderizar os links de paginação em suas views, você pode usar o método `links`.

### Exemplo de Renderização de Links

```blade
{{ $users->links() }}
```

## Customizando o View de Paginação

Você pode personalizar a aparência dos links de paginação criando sua própria view de paginação.

### Exemplo de Customização de View

Crie um arquivo de view em `resources/views/vendor/pagination` e configure o Laravel para usá-lo.

### Exemplo de Customização do Paginator

```php
Paginator::defaultView('vendor.pagination.custom');
```

## Paginação com API Resources

Quando você está construindo APIs, a paginação é essencial para retornar dados em partes. O Laravel facilita a paginação com API Resources.

### Exemplo de Paginação com API Resources

```php
namespace App\Http\Controllers\Api;

use App\Models\User;
use App\Http\Resources\UserResource;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index()
    {
        $users = User::paginate(15);
        return UserResource::collection($users);
    }
}
```

### Exemplo de Resposta JSON Paginada

```json
{
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com",
            // outros atributos
        }
        // outros registros
    ],
    "links": {
        "first": "http://example.com/api/users?page=1",
        "last": "http://example.com/api/users?page=10",
        "prev": null,
        "next": "http://example.com/api/users?page=2"
    },
    "meta": {
        "current_page": 1,
        "from": 1,
        "last_page": 10,
        "path": "http://example.com/api/users",
        "per_page": 15,
        "to": 15,
        "total": 150
    }
}
```

## Resumo

A paginação é uma funcionalidade essencial para gerenciar grandes conjuntos de dados de forma eficiente. O Laravel oferece suporte abrangente para paginação tanto em views quanto em APIs, facilitando a implementação e personalização conforme necessário.

```
