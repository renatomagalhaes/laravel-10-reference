# Testes de Autorização

Os testes de autorização são cruciais para garantir que sua aplicação está protegendo corretamente os recursos e permitindo apenas o acesso autorizado. No Laravel, você pode usar os recursos de teste integrados para verificar suas políticas e gates de autorização.

## Introdução aos Testes de Autorização

### Por que Testar Autorização?

Testar autorização é essencial para garantir que as regras de acesso são aplicadas corretamente, evitando que usuários não autorizados acessem recursos sensíveis ou executem ações proibidas.

### Benefícios dos Testes de Autorização

- **Segurança:** Garante que apenas usuários autorizados possam acessar certos recursos.
- **Confiabilidade:** Verifica se as regras de autorização funcionam conforme o esperado.
- **Manutenção:** Facilita a manutenção e a atualização das regras de autorização.

## Testando Políticas de Autorização

### Configuração de Testes

Use o Artisan para gerar um teste de política:

```bash
php artisan make:test PostPolicyTest
```

### Escrevendo Testes de Políticas

Abra o arquivo de teste gerado em `tests/Feature/PostPolicyTest.php` e escreva os testes para verificar as políticas de autorização:

```php
namespace Tests\Feature;

use App\Models\Post;
use App\Models\User;
use Tests\TestCase;
use Illuminate\Foundation\Testing\RefreshDatabase;

class PostPolicyTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function user_can_view_own_post()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create(['user_id' => $user->id]);

        $this->assertTrue($user->can('view', $post));
    }

    /** @test */
    public function user_cannot_view_others_post()
    {
        $user = User::factory()->create();
        $post = Post::factory()->create();

        $this->assertFalse($user->can('view', $post));
    }
}
```

### Executando Testes

Execute os testes usando PHPUnit:

```bash
vendor/bin/phpunit --filter=PostPolicyTest
```

## Testando Gates de Autorização

### Configuração de Testes

Use o Artisan para gerar um teste de gate:

```bash
php artisan make:test GateTest
```

### Escrevendo Testes de Gates

Abra o arquivo de teste gerado em `tests/Feature/GateTest.php` e escreva os testes para verificar os gates de autorização:

```php
namespace Tests\Feature;

use App\Models\User;
use Tests\TestCase;
use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Testing\RefreshDatabase;

class GateTest extends TestCase
{
    use RefreshDatabase;

    /** @test */
    public function admin_can_access_dashboard()
    {
        $admin = User::factory()->create(['is_admin' => true]);

        Gate::define('view-dashboard', function ($user) {
            return $user->is_admin;
        });

        $this->assertTrue($admin->can('view-dashboard'));
    }

    /** @test */
    public function non_admin_cannot_access_dashboard()
    {
        $user = User::factory()->create(['is_admin' => false]);

        Gate::define('view-dashboard', function ($user) {
            return $user->is_admin;
        });

        $this->assertFalse($user->can('view-dashboard'));
    }
}
```

### Executando Testes

Execute os testes usando PHPUnit:

```bash
vendor/bin/phpunit --filter=GateTest
```

## Exemplos Práticos

### Teste de Política de Postagem

#### Configuração de Testes

Gere o teste:

```bash
php artisan make:test PostPolicyTest
```

#### Escrevendo Testes de Políticas

Escreva os testes no arquivo `tests/Feature/PostPolicyTest.php`:

```php
/** @test */
public function user_can_update_own_post()
{
    $user = User::factory()->create();
    $post = Post::factory()->create(['user_id' => $user->id]);

    $this->assertTrue($user->can('update', $post));
}

public function user_cannot_update_others_post()
{
    $user = User::factory()->create();
    $post = Post::factory()->create();

    $this->assertFalse($user->can('update', $post));
}
```

### Teste de Gate para Acesso ao Dashboard

#### Configuração de Testes

Gere o teste:

```bash
php artisan make:test GateTest
```

#### Escrevendo Testes de Gates

Escreva os testes no arquivo `tests/Feature/GateTest.php`:

```php
/** @test */
public function admin_can_access_dashboard()
{
    $admin = User::factory()->create(['is_admin' => true]);

    Gate::define('view-dashboard', function ($user) {
        return $user->is_admin;
    });

    $this->assertTrue($admin->can('view-dashboard'));
}

public function non_admin_cannot_access_dashboard()
{
    $user = User::factory()->create(['is_admin' => false]);

    Gate::define('view-dashboard', function ($user) {
        return $user->is_admin;
    });

    $this->assertFalse($user->can('view-dashboard'));
}
```

## Resumo

Testes de autorização são essenciais para garantir a segurança e a confiabilidade da sua aplicação. No Laravel, você pode testar políticas e gates de autorização para verificar se as permissões estão sendo aplicadas corretamente. Usando os recursos de teste integrados, você pode escrever testes eficazes que garantem que somente usuários autorizados possam acessar recursos e executar ações sensíveis.
