# API Resources

Os API Resources do Laravel transformam seus modelos e coleções de modelos em JSON de forma elegante, facilitando a construção de APIs robustas.

## Definindo um API Resource

Para criar um API Resource, utilize o comando Artisan `make:resource`.

### Exemplo de Criação de Resource

```bash
php artisan make:resource UserResource
```

Isso criará um arquivo `UserResource.php` em `app/Http/Resources`.

## Estrutura de um API Resource

Um API Resource contém um método `toArray` que define como os atributos do modelo serão transformados.

### Exemplo de API Resource

```php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    /**
     * Transform the resource into an array.
     *
     * @param \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at->toDateTimeString(),
            'updated_at' => $this->updated_at->toDateTimeString(),
        ];
    }
}
```

## Usando API Resources

Você pode usar um API Resource em controladores para transformar e retornar respostas JSON.

### Exemplo de Uso de API Resource em um Controlador

```php
namespace App\Http\Controllers;

use App\Models\User;
use App\Http\Resources\UserResource;

class UserController extends Controller
{
    public function show($id)
    {
        $user = User::findOrFail($id);
        return new UserResource($user);
    }

    public function index()
    {
        $users = User::all();
        return UserResource::collection($users);
    }
}
```

## Coleções de Resources

Para transformar coleções de modelos, você pode usar o método `collection`.

### Exemplo de Coleção de Resources

```php
namespace App\Http\Controllers;

use App\Models\User;
use App\Http\Resources\UserResource;

class UserController extends Controller
{
    public function index()
    {
        $users = User::all();
        return UserResource::collection($users);
    }
}
```

## Personalizando a Estrutura JSON

Você pode personalizar a estrutura do JSON retornado por um API Resource, incluindo metadados adicionais.

### Exemplo de Personalização

```php
namespace App\Http\Resources;

use Illuminate\Http\Resources\Json\JsonResource;

class UserResource extends JsonResource
{
    /**
     * Transform the resource into an array.
     *
     * @param \Illuminate\Http\Request  $request
     * @return array
     */
    public function toArray($request)
    {
        return [
            'id' => $this->id,
            'name' => $this->name,
            'email' => $this->email,
            'created_at' => $this->created_at->toDateTimeString(),
            'updated_at' => $this->updated_at->toDateTimeString(),
        ];
    }

    /**
     * Get additional data that should be returned with the resource array.
     *
     * @param \Illuminate\Http\Request  $request
     * @return array
     */
    public function with($request)
    {
        return [
            'meta' => [
                'key' => 'value',
            ],
        ];
    }
}
```

## Resumo

Os API Resources do Laravel são uma ferramenta poderosa para transformar modelos e coleções em respostas JSON bem formatadas e consistentes, facilitando a criação de APIs robustas e escaláveis.
