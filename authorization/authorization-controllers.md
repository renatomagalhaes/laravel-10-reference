# Autorização em Controladores

No Laravel, a autorização em controladores permite proteger ações específicas, verificando se o usuário tem permissões adequadas antes de executar a lógica do controlador. Este guia aborda como implementar autorização em controladores utilizando políticas e gates.

## Introdução à Autorização em Controladores

### O que é Autorização em Controladores?

A autorização em controladores é o processo de verificar as permissões dos usuários diretamente dentro dos métodos do controlador. Isso garante que somente usuários autorizados possam executar certas ações, como visualizar, criar, atualizar ou excluir recursos.

### Benefícios da Autorização em Controladores

- **Segurança:** Protege ações críticas e recursos sensíveis.
- **Controle Granular:** Permite verificar permissões para ações específicas dentro de um controlador.
- **Facilidade de Manutenção:** Centraliza a lógica de autorização dentro do controlador.

## Usando Políticas em Controladores

### Criando uma Política

Crie uma política usando o comando Artisan:

```bash
php artisan make:policy PostPolicy --model=Post
```

### Definindo Métodos de Autorização na Política

Defina os métodos de autorização na política:

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function view(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}
```

### Registrando a Política

Registre a política no `AuthServiceProvider`:

```php
namespace App\Providers;

use App\Models\Post;
use App\Policies\PostPolicy;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        Post::class => PostPolicy::class,
    ];

    public function boot()
    {
        $this->registerPolicies();
    }
}
```

### Usando a Política no Controlador

Utilize o método `authorize` nos métodos do controlador para verificar a autorização:

```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function show(Post $post)
    {
        $this->authorize('view', $post);

        return view('posts.show', compact('post'));
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // Lógica de atualização do post
    }
}
```

## Usando Gates em Controladores

### Definindo um Gate

Defina um gate no `AuthServiceProvider`:

```php
use Illuminate\Support\Facades\Gate;

Gate::define('update-post', function ($user, $post) {
    return $user->id === $post->user_id;
});
```

### Usando o Gate no Controlador

Utilize o método `Gate::allows` ou `Gate::denies` nos métodos do controlador para verificar a autorização:

```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Gate;

class PostController extends Controller
{
    public function update(Request $request, Post $post)
    {
        if (Gate::denies('update-post', $post)) {
            abort(403);
        }

        // Lógica de atualização do post
    }
}
```

## Exemplos Práticos

### Exemplo de Política de Postagem

#### Criando a Política

```bash
php artisan make:policy PostPolicy --model=Post
```

#### Definindo a Política

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function view(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}
```

#### Registrando a Política

```php
namespace App\Providers;

use App\Models\Post;
use App\Policies\PostPolicy;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        Post::class => PostPolicy::class,
    ];

    public function boot()
    {
        $this->registerPolicies();
    }
}
```

#### Usando a Política no Controlador

```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function show(Post $post)
    {
        $this->authorize('view', $post);

        return view('posts.show', compact('post'));
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // Lógica de atualização do post
    }
}
```

## Resumo

A autorização em controladores no Laravel permite proteger ações específicas verificando as permissões dos usuários diretamente nos métodos do controlador. Usando políticas e gates, você pode garantir que somente usuários autorizados possam executar certas ações, aumentando a segurança e o controle granular sobre o acesso aos recursos da aplicação.
