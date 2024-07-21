# Controle de Sessão

O controle de sessão permite que os usuários visualizem e gerenciem suas sessões de login em diferentes dispositivos. Isso é útil para melhorar a segurança, permitindo que os usuários desconectem dispositivos não reconhecidos ou saiam de todas as sessões ativas.

## Configurando o Controle de Sessão

### Passo 1: Configuração do Session Driver

Certifique-se de que você está usando um driver de sessão que suporte múltiplas sessões, como `database`. Configure o driver de sessão no arquivo `.env`:

```env
SESSION_DRIVER=database
```

### Passo 2: Criar a Tabela de Sessões

Execute a migração para criar a tabela de sessões:

```bash
php artisan session:table
php artisan migrate
```

### Passo 3: Middleware de Controle de Sessão

Adicione middleware para garantir que os usuários estejam autenticados e para verificar suas sessões ativas.

### Exemplo de Middleware

```php
namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class EnsureSessionIsValid
{
    public function handle(Request $request, Closure $next)
    {
        if (Auth::check()) {
            $currentSessionId = session()->getId();
            $userSessions = \DB::table('sessions')
                ->where('user_id', Auth::id())
                ->where('id', '!=', $currentSessionId)
                ->get();

            foreach ($userSessions as $session) {
                if ($this->isSessionExpired($session)) {
                    \DB::table('sessions')->where('id', $session->id)->delete();
                }
            }
        }

        return $next($request);
    }

    protected function isSessionExpired($session)
    {
        $lastActivity = $session->last_activity;
        $timeout = config('session.lifetime') * 60;

        return now()->timestamp - $lastActivity > $timeout;
    }
}
```

### Passo 4: Visualização das Sessões Ativas

Crie uma rota e um controlador para permitir que os usuários visualizem suas sessões ativas.

#### Exemplo de Rota

```php
Route::get('/user/sessions', 'SessionController@index')->middleware('auth');
```

#### Exemplo de Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class SessionController extends Controller
{
    public function index()
    {
        $sessions = \DB::table('sessions')
            ->where('user_id', Auth::id())
            ->get();

        return view('user.sessions', ['sessions' => $sessions]);
    }
}
```

### Passo 5: Desconectar de Sessões Específicas

Adicione funcionalidade para permitir que os usuários desconectem de sessões específicas.

#### Exemplo de Rota e Controlador para Logout

```php
Route::delete('/user/sessions/{sessionId}', 'SessionController@destroy')->middleware('auth');

public function destroy($sessionId)
{
    \DB::table('sessions')->where('id', $sessionId)->delete();
    return redirect('/user/sessions')->with('status', 'Sessão desconectada com sucesso.');
}
```

### Passo 6: Desconectar de Todas as Sessões

Adicione funcionalidade para permitir que os usuários desconectem de todas as sessões ativas.

#### Exemplo de Rota e Controlador para Logout de Todas as Sessões

```php
Route::post('/user/sessions/logout-all', 'SessionController@logoutAll')->middleware('auth');

public function logoutAll()
{
    \DB::table('sessions')->where('user_id', Auth::id())->delete();
    Auth::logout();
    return redirect('/login')->with('status', 'Desconectado de todas as sessões com sucesso.');
}
```

## Resumo

O controle de sessão no Laravel permite que os usuários visualizem e gerenciem suas sessões de login, melhorando a segurança ao permitir que desconectem dispositivos não reconhecidos ou saiam de todas as sessões ativas. Utilizando o driver de sessão adequado e configurando rotas e controladores apropriados, você pode implementar essa funcionalidade de forma eficiente.
