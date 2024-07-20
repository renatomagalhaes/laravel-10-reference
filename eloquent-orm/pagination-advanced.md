# Paginação Avançada

Quando lidamos com milhões de registros, a paginação convencional pode se tornar ineficiente devido à necessidade de contar todos os registros ou pular muitos deles. Uma abordagem mais eficiente é usar ponteiros, que permitem paginar os resultados de maneira mais performática.

## Paginação Baseada em Ponteiros

Em vez de usar offsets, a paginação baseada em ponteiros utiliza um marcador (como um ID) para indicar a posição de início da próxima página. Essa técnica é muito mais eficiente em bancos de dados grandes.

### Exemplo de Paginação com Ponteiros

```php
namespace App\Http\Controllers;

use App\Models\User;
use Illuminate\Http\Request;

class UserController extends Controller
{
    public function index(Request $request)
    {
        $perPage = 15;
        $lastId = $request->input('last_id');

        $query = User::orderBy('id', 'asc');

        if ($lastId) {
            $query->where('id', '>', $lastId);
        }

        $users = $query->limit($perPage + 1)->get();

        $nextLastId = null;
        if ($users->count() > $perPage) {
            $nextLastId = $users->last()->id;
            $users = $users->slice(0, $perPage);
        }

        return view('users.index', [
            'users' => $users,
            'next_last_id' => $nextLastId,
        ]);
    }
}
```

## Considerações de Performance

### Índices

Certifique-se de que há índices nos campos usados para ordenação e filtragem, como o campo `id`.

### Ordenação

A ordenação é crucial para garantir que os registros sejam recuperados de forma eficiente e na ordem correta.

## Implementação em APIs

Para APIs, a abordagem é similar, mas você retornará os ponteiros como parte da resposta JSON.

### Exemplo de API com Paginação Baseada em Ponteiros

```php
namespace App\Http\Controllers\Api;

use App\Models\User;
use Illuminate\Http\Request;
use App\Http\Resources\UserResource;

class UserController extends Controller
{
    public function index(Request $request)
    {
        $perPage = 15;
        $lastId = $request->input('last_id');

        $query = User::orderBy('id', 'asc');

        if ($lastId) {
            $query->where('id', '>', $lastId);
        }

        $users = $query->limit($perPage + 1)->get();

        $nextLastId = null;
        if ($users->count() > $perPage) {
            $nextLastId = $users->last()->id;
            $users = $users->slice(0, $perPage);
        }

        return UserResource::collection($users)->additional([
            'meta' => [
                'next_last_id' => $nextLastId,
            ],
        ]);
    }
}
```

### Exemplo de Resposta JSON

```json
{
    "data": [
        {
            "id": 1,
            "name": "John Doe",
            "email": "john@example.com"
        }
        // outros registros
    ],
    "meta": {
        "next_last_id": 101
    }
}
```

## Resumo

A paginação avançada baseada em ponteiros é uma técnica eficiente para lidar com grandes conjuntos de dados, evitando os problemas de performance associados à paginação tradicional. Ao utilizar índices apropriados e ordenar corretamente os resultados, você pode melhorar significativamente o desempenho de sua aplicação.
