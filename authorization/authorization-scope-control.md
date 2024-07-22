# Autorização com Controle de Escopo

A autorização com controle de escopo permite definir níveis de acesso detalhados e específicos para diferentes partes da aplicação. No Laravel, você pode implementar controles de escopo para garantir que os usuários tenham apenas as permissões necessárias para realizar suas tarefas.

## Introdução ao Controle de Escopo

### O que é Controle de Escopo?

O controle de escopo refere-se à prática de limitar as permissões de usuários a determinadas ações ou recursos específicos dentro da aplicação. Isso é útil para aplicar políticas de menor privilégio e garantir que os usuários não tenham mais permissões do que o necessário.

### Benefícios do Controle de Escopo

- **Segurança:** Minimiza o risco de abuso de permissões.
- **Precisão:** Permite definir permissões detalhadas e específicas para diferentes usuários e funções.
- **Flexibilidade:** Facilita a adaptação das permissões de acordo com as necessidades específicas dos usuários.

## Implementando Controle de Escopo

### Passo 1: Definir Escopos de Permissões

Comece definindo os diferentes escopos de permissões que você precisa na sua aplicação. Por exemplo, você pode ter escopos como "view-reports", "manage-users", "edit-settings", etc.

### Passo 2: Adicionar Escopos ao Modelo User

Adicione uma coluna `scopes` ao modelo `User` para armazenar os escopos de permissões:

```php
Schema::table('users', function (Blueprint $table) {
    $table->json('scopes')->nullable();
});
```

### Passo 3: Definir Métodos para Verificação de Escopos

Adicione métodos ao modelo `User` para verificar os escopos:

```php
namespace App\Models;

use Illuminate\Foundation\Auth\User as Authenticatable;
use Illuminate\Notifications\Notifiable;

class User extends Authenticatable
{
    use Notifiable;

    public function hasScope($scope)
    {
        return in_array($scope, $this->scopes ?? []);
    }
}
```

### Passo 4: Definir Gates com Escopos

Defina gates que verificam os escopos dos usuários:

```php
namespace App\Providers;

use Illuminate\Support\Facades\Gate;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider
{
    public function boot()
    {
        $this->registerPolicies();

        Gate::define('view-reports', function ($user) {
            return $user->hasScope('view-reports');
        });

        Gate::define('manage-users', function ($user) {
            return $user->hasScope('manage-users');
        });
    }
}
```

### Passo 5: Usar Gates com Escopos em Controladores

Verifique os escopos dos usuários nos controladores:

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Gate;

class ReportController extends Controller
{
    public function index()
    {
        if (Gate::denies('view-reports')) {
            abort(403);
        }

        // Lógica para exibir relatórios
    }
}
```

### Passo 6: Usar Gates com Escopos em Vistas

Use a diretiva `@can` nas views Blade para verificar os escopos:

```blade
@can('view-reports')
    <a href="{{ route('reports.index') }}">Ver Relatórios</a>
@endcan
```

## Exemplos Práticos

### Definindo e Usando Escopos de Permissões

#### Adicionando Escopos ao Modelo User

```php
Schema::table('users', function (Blueprint $table) {
    $table->json('scopes')->nullable();
});
```

Adicione escopos ao usuário:

```php
$user = User::find(1);
$user->scopes = ['view-reports', 'manage-users'];
$user->save();
```

#### Verificando Escopos nos Controladores

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Gate;

class UserController extends Controller
{
    public function manage()
    {
        if (Gate::denies('manage-users')) {
            abort(403);
        }

        // Lógica para gerenciar usuários
    }
}
```

## Resumo

O controle de escopo no Laravel permite definir permissões detalhadas e específicas para diferentes partes da aplicação, garantindo que os usuários tenham apenas as permissões necessárias para realizar suas tarefas. Usando escopos, você pode implementar políticas de menor privilégio e melhorar a segurança e a precisão das permissões na sua aplicação.
