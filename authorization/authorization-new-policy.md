# Definindo uma nova Policy

Definir uma nova policy no Laravel permite centralizar a lógica de autorização para um modelo específico, facilitando a manutenção e a reutilização das regras de autorização. Este guia aborda como criar e usar uma nova policy no Laravel.

## Introdução à Policy

### O que é uma Policy?

Uma policy é uma classe dedicada que organiza a lógica de autorização em torno de um modelo específico. As policies fornecem métodos para verificar se um usuário pode realizar ações como visualizar, criar, atualizar ou excluir um recurso.

### Benefícios de Usar Policies

- **Centralização:** Mantém a lógica de autorização em um único lugar.
- **Organização:** Facilita a manutenção e a leitura do código.
- **Reutilização:** Permite reutilizar a lógica de autorização em diferentes partes da aplicação.

## Criando uma Nova Policy

### Passo 1: Gerar a Policy

Use o comando Artisan `make:policy` para gerar uma nova policy:

```bash
php artisan make:policy PostPolicy --model=Post
```

### Passo 2: Definir Métodos de Autorização

Abra o arquivo gerado em `app/Policies/PostPolicy.php` e defina os métodos de autorização:

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;

    public function view(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function create(User $user)
    {
        return $user->role === 'editor';
    }

    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function delete(User $user, Post $post)
    {
        return $user->id === $post->user_id && $post->status !== 'published';
    }
}
```

### Passo 3: Registrar a Policy

Registre a policy no `AuthServiceProvider`:

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

## Usando Policies

### Autorização em Controladores

Use o método `authorize` nos métodos do controlador para verificar a autorização:

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

    public function store(Request $request)
    {
        $this->authorize('create', Post::class);

        // Lógica para criar o post
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // Lógica de atualização do post
    }

    public function destroy(Post $post)
    {
        $this->authorize('delete', $post);

        // Lógica para deletar o post
    }
}
```

### Autorização em Vistas

Use a diretiva `@can` nas views Blade para verificar a autorização:

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@endcan

@can('delete', $post)
    <form action="{{ route('posts.destroy', $post) }}" method="POST">
        @csrf
        @method('DELETE')
        <button type="submit">Deletar</button>
    </form>
@endcan
```

## Exemplos Práticos

### Definindo e Usando uma Policy para Postagens

#### Criando a Policy

```bash
php artisan make:policy PostPolicy --model=Post
```

#### Definindo a Policy

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

    public function create(User $user)
    {
        return $user->role === 'editor';
    }

    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }

    public function delete(User $user, Post $post)
    {
        return $user->id === $post->user_id && $post->status !== 'published';
    }
}
```

#### Registrando a Policy

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

#### Usando a Policy no Controlador

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

    public function store(Request $request)
    {
        $this->authorize('create', Post::class);

        // Lógica para criar o post
    }

    public function update(Request $request, Post $post)
    {
        $this->authorize('update', $post);

        // Lógica de atualização do post
    }

    public function destroy(Post $post)
    {
        $this->authorize('delete', $post);

        // Lógica para deletar o post
    }
}
```

## Resumo

Definir uma nova policy no Laravel permite centralizar a lógica de autorização, facilitando a manutenção e a reutilização das regras de autorização. Usando policies, você pode garantir que apenas usuários autorizados possam realizar ações específicas em seus modelos, aumentando a segurança e a consistência da sua aplicação.
