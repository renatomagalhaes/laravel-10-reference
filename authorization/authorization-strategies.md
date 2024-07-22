# Estrategias de Autorização

Implementar estratégias de autorização eficazes é crucial para garantir que sua aplicação Laravel seja segura e escalável. Este guia aborda diferentes estratégias de autorização e como aplicá-las na sua aplicação.

## Introdução às Estrategias de Autorização

### O que são Estrategias de Autorização?

Estratégias de autorização são abordagens e técnicas utilizadas para definir, organizar e gerenciar as permissões e acessos dos usuários em uma aplicação. Elas ajudam a garantir que apenas usuários autorizados possam acessar recursos específicos ou executar determinadas ações.

### Benefícios das Estrategias de Autorização

- **Segurança:** Garante que apenas usuários autorizados possam acessar recursos sensíveis.
- **Escalabilidade:** Facilita a gestão de permissões em aplicações grandes e complexas.
- **Manutenção:** Torna mais fácil adicionar, remover ou modificar permissões conforme necessário.

## Tipos de Estrategias de Autorização

### Controle de Acesso Baseado em Funções (RBAC)

RBAC é uma estratégia de autorização onde as permissões são atribuídas a funções, e os usuários recebem essas funções. Isso facilita a gestão de permissões em aplicações com muitos usuários.

#### Implementação de RBAC

Crie tabelas para funções e permissões:

```php
Schema::create('roles', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});

Schema::create('permissions', function (Blueprint $table) {
    $table->id();
    $table->string('name');
    $table->timestamps();
});

Schema::create('role_user', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->timestamps();
});

Schema::create('permission_role', function (Blueprint $table) {
    $table->id();
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->foreignId('permission_id')->constrained()->onDelete('cascade');
    $table->timestamps();
});
```

Defina os modelos e relacionamentos:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Role extends Model
{
    use HasFactory;

    public function permissions()
    {
        return $this->belongsToMany(Permission::class);
    }

    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}

class Permission extends Model
{
    use HasFactory;

    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}
```

Adicione métodos ao modelo User:

```php
namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use Notifiable;

    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }

    public function hasRole($role)
    {
        return $this->roles()->where('name', $role)->exists();
    }

    public function hasPermission($permission)
    {
        foreach ($this->roles as $role) {
            if ($role->permissions()->where('name', $permission)->exists()) {
                return true;
            }
        }
        return false;
    }
}
```

### Controle de Acesso Baseado em Políticas (PBAC)

PBAC utiliza políticas para definir regras de autorização. As políticas são classes que contêm métodos para verificar se um usuário pode realizar uma ação específica em um recurso.

#### Implementação de PBAC

Crie uma política usando o comando Artisan:

```bash
php artisan make:policy PostPolicy --model=Post
```

Defina a política:

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

Registre a política no `AuthServiceProvider`:

```php
protected $policies = [
    Post::class => PostPolicy::class,
];
```

### Controle de Acesso Baseado em Regras (Rule-Based Access Control)

Essa estratégia utiliza regras definidas dinamicamente para determinar se um usuário tem permissão para realizar uma ação. É útil em situações onde as permissões precisam ser verificadas com base em condições dinâmicas.

#### Implementação de Regras Dinâmicas

Defina uma regra personalizada:

```php
namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class CustomAccessRule implements Rule
{
    public function passes($attribute, $value)
    {
        // Lógica personalizada de autorização
        return $value > 100;
    }

    public function message()
    {
        return 'Você não tem permissão para realizar esta ação.';
    }
}
```

Use a regra em uma política ou controlador:

```php
use App\Rules\CustomAccessRule;

public function store(Request $request)
{
    $request->validate([
        'amount' => ['required', new CustomAccessRule],
    ]);

    // Lógica de armazenamento
}
```

## Resumo

Implementar estratégias de autorização no Laravel é essencial para garantir a segurança e a integridade da sua aplicação. Utilizando RBAC, PBAC e regras dinâmicas, você pode definir e gerenciar permissões de maneira eficaz e escalável, garantindo que apenas usuários autorizados possam acessar recursos e executar ações sensíveis.
