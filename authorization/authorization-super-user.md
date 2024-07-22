# Super Usuário

O conceito de super usuário na autorização refere-se a um usuário que possui permissões elevadas, permitindo-lhe realizar ações que outros usuários não podem. Este guia aborda como implementar um super usuário no Laravel para facilitar a administração e manutenção da aplicação.

## Introdução ao Super Usuário

### O que é um Super Usuário?

Um super usuário é um usuário com permissões especiais que permitem acesso completo ou quase completo à aplicação. Eles podem realizar ações administrativas, ignorar regras de autorização e acessar todos os recursos.

### Benefícios do Super Usuário

- **Flexibilidade:** Permite que administradores realizem qualquer ação necessária sem restrições.
- **Facilidade de Administração:** Facilita a manutenção e o gerenciamento da aplicação.
- **Segurança:** Pode ser usado como uma camada adicional de segurança para operações críticas.

## Implementando Super Usuário

### Adicionando o Papel de Super Usuário

Crie um papel específico para super usuários:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateRolesTable extends Migration
{
    public function up()
    {
        Schema::create('roles', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->timestamps();
        });

        // Adicione o papel de super usuário
        DB::table('roles')->insert([
            'name' => 'super-admin',
        ]);
    }

    public function down()
    {
        Schema::dropIfExists('roles');
    }
}
```

### Definindo o Super Usuário no Modelo User

Adicione métodos ao modelo `User` para verificar se o usuário é um super usuário:

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

    public function isSuperAdmin()
    {
        return $this->roles()->where('name', 'super-admin')->exists();
    }
}
```

### Ajustando Políticas e Gates para Super Usuário

Modifique as políticas e gates para levar em conta o super usuário:

```php
namespace App\Policies;

use App\Models\Post;
use App\Models\User;

class PostPolicy
{
    public function before(User $user, $ability)
    {
        if ($user->isSuperAdmin()) {
            return true;
        }
    }

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

## Usando Super Usuário em Controladores e Vistas

### Verificando Super Usuário em Controladores

```php
namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    public function update(Request $request, Post $post)
    {
        if ($request->user()->cannot('update', $post)) {
            abort(403);
        }

        // Lógica de atualização do post
    }
}
```

### Verificando Super Usuário em Vistas

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@endcan

@if(auth()->user()->isSuperAdmin())
    <a href="{{ route('posts.delete', $post) }}">Deletar</a>
@endif
```

## Exemplos Práticos

### Implementando um Controle Completo de Super Usuário

#### Definindo o Papel e Adicionando o Método de Verificação

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class AddSuperAdminRole extends Migration
{
    public function up()
    {
        Schema::table('roles', function (Blueprint $table) {
            $table->boolean('is_super_admin')->default(false);
        });

        // Atualize o papel de super-admin
        DB::table('roles')->where('name', 'super-admin')->update([
            'is_super_admin' => true,
        ]);
    }

    public function down()
    {
        Schema::table('roles', function (Blueprint $table) {
            $table->dropColumn('is_super_admin');
        });
    }
}
```

#### Ajustando o Modelo User

```php
public function isSuperAdmin()
{
    return $this->roles()->where('is_super_admin', true)->exists();
}
```

#### Modificando Políticas e Gates

```php
public function before(User $user, $ability)
{
    if ($user->isSuperAdmin()) {
        return true;
    }
}
```

## Resumo

Implementar um super usuário no Laravel permite que administradores tenham acesso completo à aplicação, facilitando a administração e manutenção. Usando papéis, políticas e gates, você pode definir e verificar permissões de super usuário, garantindo uma aplicação flexível e segura.
