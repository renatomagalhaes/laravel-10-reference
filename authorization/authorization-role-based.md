# Autorização baseada em Funções

A autorização baseada em funções permite controlar o acesso a recursos e ações na aplicação com base nas funções atribuídas aos usuários. Este método é útil para aplicações que têm diferentes níveis de acesso e permissões.

## Introdução à Autorização baseada em Funções

### O que é Autorização baseada em Funções?

A autorização baseada em funções é um método de controle de acesso onde os usuários são atribuídos a uma ou mais funções, e cada função possui um conjunto de permissões que determinam o que o usuário pode ou não fazer na aplicação.

### Benefícios da Autorização baseada em Funções

- **Escalabilidade:** Facilita a gestão de permissões em aplicações grandes.
- **Flexibilidade:** Permite definir diferentes níveis de acesso e permissões.
- **Organização:** Centraliza a lógica de autorização em torno de funções específicas.

## Definindo Funções e Permissões

### Estrutura do Banco de Dados

Crie tabelas para armazenar as funções e permissões dos usuários:

```php
Schema::create('roles', function (Blueprint $table) {
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
```

### Modelo Role

Crie um modelo para as funções:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Role extends Model
{
    use HasFactory;

    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

### Relacionamento no Modelo User

Adicione o relacionamento de funções no modelo `User`:

```php
namespace App\Models;

use Illuminate\Contracts\Auth\MustVerifyEmail;
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
}
```

## Usando Funções para Autorização

### Verificando Funções no Controlador

Use o método `hasRole` para verificar se o usuário tem uma função específica:

```php
public function index()
{
    if (auth()->user()->hasRole('admin')) {
        // O usuário é um administrador
    } else {
        abort(403, 'Ação não autorizada.');
    }
}
```

### Autorização em Vistas

Use a diretiva `@can` ou `@hasrole` para verificar permissões nas vistas Blade:

```blade
@hasrole('admin')
    <a href="{{ route('admin.dashboard') }}">Dashboard Admin</a>
@endhasrole
```

### Middleware baseado em Funções

Crie um middleware para verificar as funções dos usuários:

```php
namespace App\Http\Middleware;

use Closure;

class RoleMiddleware
{
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            abort(403, 'Ação não autorizada.');
        }

        return $next($request);
    }
}
```

Registre o middleware no arquivo `Kernel.php`:

```php
protected $routeMiddleware = [
    // Outros middlewares
    'role' => \App\Http\Middleware\RoleMiddleware::class,
];
```

Use o middleware nas rotas:

```php
Route::get('/admin', [AdminController::class, 'index'])->middleware('role:admin');
```

## Exemplos Práticos

### Função de Administrador

#### Definindo a Função de Administrador

```php
Role::create(['name' => 'admin']);
```

#### Atribuindo a Função ao Usuário

```php
$user = User::find(1);
$user->roles()->attach(Role::where('name', 'admin')->first());
```

#### Verificando a Função no Controlador

```php
public function index()
{
    if (auth()->user()->hasRole('admin')) {
        // Lógica do administrador
    } else {
        abort(403, 'Ação não autorizada.');
    }
}
```

## Resumo

A autorização baseada em funções no Laravel permite definir e gerenciar permissões de maneira organizada e escalável. Usando funções e verificando permissões em controladores, vistas e middlewares, você pode criar uma aplicação segura e fácil de manter, garantindo que os usuários tenham acesso apenas aos recursos e ações permitidos por suas funções.
