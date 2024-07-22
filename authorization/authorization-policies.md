# Políticas de Autorização

As políticas de autorização no Laravel permitem centralizar a lógica de autorização para um modelo específico, facilitando a manutenção e a organização do código. Este guia aborda como criar e usar políticas de autorização no Laravel.

## Introdução às Políticas

### O que são Políticas?

Políticas são classes que organizam a lógica de autorização em torno de um modelo ou recurso específico. Elas fornecem uma maneira centralizada de definir regras de acesso, garantindo que a autorização seja consistente em toda a aplicação.

### Benefícios das Políticas

- **Centralização:** Mantém a lógica de autorização em um único lugar.
- **Organização:** Facilita a manutenção e a leitura do código.
- **Reutilização:** Permite reutilizar a lógica de autorização em diferentes partes da aplicação.

## Criando uma Política

### Passo 1: Gerar a Política

Use o comando Artisan `make:policy` para gerar uma nova política:

```bash
php artisan make:policy NomeDaPolicy
```

### Passo 2: Definir a Política

Abra o arquivo gerado em `app/Policies/NomeDaPolicy.php` e defina os métodos de autorização:

```php
namespace App\Policies;

use App\Models\User;
use App\Models\NomeDoModelo;
use Illuminate\Auth\Access\HandlesAuthorization;

class NomeDaPolicy
{
    use HandlesAuthorization;

    public function view(User $user, NomeDoModelo $modelo)
    {
        // Lógica de autorização
        return $user->id === $modelo->user_id;
    }

    public function update(User $user, NomeDoModelo $modelo)
    {
        // Lógica de autorização
        return $user->id === $modelo->user_id;
    }
}
```

### Passo 3: Registrar a Política

Registre a política no `AuthServiceProvider`:

```php
namespace App\Providers;

use App\Models\NomeDoModelo;
use App\Policies\NomeDaPolicy;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    protected $policies = [
        NomeDoModelo::class => NomeDaPolicy::class,
    ];

    public function boot()
    {
        $this->registerPolicies();
    }
}
```

## Usando Políticas

### Autorização em Controladores

Use o método `authorize` nos controladores para verificar a autorização:

```php
namespace App\Http\Controllers;

use App\Models\NomeDoModelo;
use Illuminate\Http\Request;

class NomeDoControlador extends Controller
{
    public function show(Request $request, NomeDoModelo $modelo)
    {
        $this->authorize('view', $modelo);

        // Lógica do controlador
    }

    public function update(Request $request, NomeDoModelo $modelo)
    {
        $this->authorize('update', $modelo);

        // Lógica do controlador
    }
}
```

### Autorização em Vistas

Use a diretiva `@can` nas vistas Blade para verificar a autorização:

```blade
@can('update', $modelo)
    <!-- Conteúdo autorizado -->
@endcan
```

## Exemplos Práticos

### Política de Postagem

#### Criando a Política

```bash
php artisan make:policy PostPolicy --model=Post
```

#### Definindo a Política

```php
namespace App\Policies;

use App\Models\User;
use App\Models\Post;
use Illuminate\Auth\Access\HandlesAuthorization;

class PostPolicy
{
    use HandlesAuthorization;

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

#### Usando a Política

No controlador:

```php
public function show(Request $request, Post $post)
{
    $this->authorize('view', $post);

    // Lógica do controlador
}

public function update(Request $request, Post $post)
{
    $this->authorize('update', $post);

    // Lógica do controlador
}
```

Na vista:

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@endcan
```

## Resumo

As políticas de autorização no Laravel permitem centralizar a lógica de autorização, facilitando a manutenção e a organização do código. Utilizando políticas, você pode definir regras de acesso claras e reutilizáveis, garantindo que a autorização seja consistente e segura em toda a aplicação.
