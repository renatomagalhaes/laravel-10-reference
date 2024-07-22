# Testes de Autorização

Os testes de autorização garantem que apenas usuários autorizados possam acessar determinadas funcionalidades da aplicação. Eles verificam se as políticas e gates estão sendo aplicadas corretamente para proteger os recursos da aplicação.

## Importância dos Testes de Autorização

### Benefícios

- **Segurança:** Assegura que os recursos da aplicação sejam acessados apenas por usuários autorizados.
- **Confiabilidade:** Garante que as regras de negócio de autorização sejam aplicadas corretamente.
- **Integridade:** Protege os dados sensíveis e operações críticas da aplicação.

## Estrutura Básica de um Teste de Autorização

### Exemplo de Teste de Autorização

Crie um teste para verificar o comportamento das políticas de autorização.

#### Exemplo com PHPUnit

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use App\Models\Post;
use Illuminate\Support\Facades\Gate;

class AuthorizationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserCanViewOwnPost()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        Gate::define('view-post', function ($user, $post) {
            return $user->id === $post->user_id;
        });

        $this->actingAs($user);
        $response = $this->get('/posts/'.$post->id);

        $response->assertStatus(200);
    }

    public function testUserCannotViewOthersPost()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        Gate::define('view-post', function ($user, $post) {
            return $user->id === $post->user_id;
        });

        $this->actingAs($user);
        $response = $this->get('/posts/'.$post->id);

        $response->assertStatus(403);
    }
}
```

### Exemplo com Pest

```php
use Illuminate\Support\Facades\Gate;
use App\Models\User;
use App\Models\Post;

it('allows user to view their own post', function () {
    $user = User::factory()->create();
    $post = Post::factory()->create(['user_id' => $user->id]);

    Gate::define('view-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });

    $this->actingAs($user)
         ->get('/posts/'.$post->id)
         ->assertStatus(200);
});

it('forbids user from viewing others post', function () {
    $user = User::factory()->create();
    $otherUser = User::factory()->create();
    $post = Post::factory()->create(['user_id' => $otherUser->id]);

    Gate::define('view-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });

    $this->actingAs($user)
         ->get('/posts/'.$post->id)
         ->assertStatus(403);
});
```

## Criando Testes de Autorização

### Passo 1: Configurar Políticas de Autorização

Defina as políticas de autorização no arquivo `app/Policies/PostPolicy.php`:

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

### Passo 2: Registrar Políticas

Registre as políticas no arquivo `app/Providers/AuthServiceProvider.php`:

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

### Passo 3: Implementar as Políticas

Aplique as políticas nos controladores ou middleware conforme necessário.

### Passo 4: Escrever Testes de Autorização

Escreva os testes para verificar a aplicação das políticas de autorização no arquivo `tests/Feature/AuthorizationTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use App\Models\Post;

class AuthorizationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserCanViewOwnPost()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->actingAs($user)
             ->get('/posts/'.$post->id)
             ->assertStatus(200);
    }

    public function testUserCannotViewOthersPost()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $this->actingAs($user)
             ->get('/posts/'.$post->id)
             ->assertStatus(403);
    }

    public function testUserCanUpdateOwnPost()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->actingAs($user)
             ->patch('/posts/'.$post->id, ['title' => 'Updated Title'])
             ->assertStatus(200);
    }

    public function testUserCannotUpdateOthersPost()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $this->actingAs($user)
             ->patch('/posts/'.$post->id, ['title' => 'Updated Title'])
             ->assertStatus(403);
    }
}
```

## Testes de Autorização com Gates

### Implementando Gates

Defina gates no arquivo `app/Providers/AuthServiceProvider.php`:

```php
Gate::define('view-post', function ($user, $post) {
    return $user->id === $post->user_id;
});

Gate::define('update-post', function ($user, $post) {
    return $user->id === $post->user_id;
});
```

### Teste de Gates

Escreva um teste para verificar o comportamento das gates:

```php
public function testGateAllowsUserToViewOwnPost()
{
    $user = User::factory()->create();
    $post = Post::factory()->create(['user_id' => $user->id]);

    Gate::define('view-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });

    $this->actingAs($user)
         ->get('/posts/'.$post->id)
         ->assertStatus(200);
}

public function testGateForbidsUserFromViewingOthersPost()
{
    $user = User::factory()->create();
    $otherUser = User::factory()->create();
    $post = Post::factory()->create(['user_id' => $otherUser->id]);

    Gate::define('view-post', function ($user, $post) {
        return $user->id === $post->user_id;
    });

    $this->actingAs($user)
         ->get('/posts/'.$post->id)
         ->assertStatus(403);
}
```

## Práticas Recomendadas

### Testar Cenários de Sucesso e Falha

Garanta que seus testes cubram tanto os cenários de sucesso quanto os de falha, incluindo permissões de visualização e edição.

### Usar Factories para Criar Dados de Teste

Utilize factories para criar usuários e outros dados de teste de forma consistente.

### Verificar Respostas e Mensagens de Erro

Teste as respostas e mensagens de erro para garantir que os usuários recebam feedback adequado ao tentarem acessar recursos não autorizados.

## Exemplo Completo

### Políticas de Autorização

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

### Teste de Autorização

No arquivo `tests/Feature/AuthorizationTest.php`, escreva o teste de autorização:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use App\Models\Post;

class AuthorizationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserCanViewOwnPost()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->actingAs($user)
             ->get('/posts/'.$post->id)
             ->assertStatus(200);
    }

    public function testUserCannotViewOthersPost()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $this->actingAs($user)
             ->get('/posts/'.$post->id)
             ->assertStatus(403);
    }

    public function testUserCanUpdateOwnPost()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->actingAs($user)
             ->patch('/posts/'.$post->id, ['title' => 'Updated Title'])
             ->assertStatus(200);
    }

    public function testUserCannotUpdateOthersPost()
    {
        $user = User::factory()->create();
        $otherUser = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $otherUser->id]);

        $this->actingAs($user)
             ->patch('/posts/'.$post->id, ['title' => 'Updated Title'])
             ->assertStatus(403);
    }
}
```

## Resumo

Os testes de autorização são essenciais para garantir que apenas usuários autorizados possam acessar e modificar determinados recursos na aplicação. Com PHPUnit e Pest, você pode escrever testes de autorização eficientes que garantem a segurança e a integridade dos dados na sua aplicação Laravel.
