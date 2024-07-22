# Testes de Autenticação

Os testes de autenticação são cruciais para garantir que o processo de login, registro e proteção de rotas funcionem corretamente. Eles verificam se os usuários podem se autenticar e acessar as partes da aplicação que requerem autenticação.

## Importância dos Testes de Autenticação

### Benefícios

- **Segurança:** Garante que somente usuários autenticados possam acessar certas rotas.
- **Confiabilidade:** Assegura que o processo de autenticação funcione corretamente.
- **Integridade dos Dados:** Verifica que os dados do usuário são protegidos e manipulados corretamente.

## Estrutura Básica de um Teste de Autenticação

### Exemplo de Teste de Autenticação

Crie um teste para verificar o comportamento do processo de login de usuários.

#### Exemplo com PHPUnit

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    public function testLoginUser()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'password',
        ]);

        $response->assertRedirect('/home');
        $this->assertAuthenticatedAs($user);
    }

    public function testLoginFailsWithInvalidCredentials()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'invalid-password',
        ]);

        $response->assertSessionHasErrors('email');
        $this->assertGuest();
    }
}
```

### Exemplo com Pest

```php
it('logs in a user', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'password',
    ]);

    $response->assertRedirect('/home');
    expect(auth()->user()->email)->toBe('test@example.com');
});

it('fails to log in with invalid credentials', function () {
    $user = User::factory()->create([
        'email' => 'test@example.com',
        'password' => bcrypt('password'),
    ]);

    $response = $this->post('/login', [
        'email' => 'test@example.com',
        'password' => 'invalid-password',
    ]);

    $response->assertSessionHasErrors('email');
    expect(auth()->check())->toBeFalse();
});
```

## Criando Testes de Autenticação

### Passo 1: Configurar Rotas de Autenticação

Defina as rotas de autenticação no arquivo `routes/web.php`:

```php
use App\Http\Controllers\Auth\LoginController;

Route::get('/login', [LoginController::class, 'showLoginForm'])->name('login');
Route::post('/login', [LoginController::class, 'login']);
Route::post('/logout', [LoginController::class, 'logout'])->name('logout');
```

### Passo 2: Implementar o Controlador

Implemente o controlador `LoginController` para lidar com o processo de login:

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class LoginController extends Controller
{
    public function showLoginForm()
    {
        return view('auth.login');
    }

    public function login(Request $request)
    {
        $credentials = $request->validate([
            'email' => ['required', 'email'],
            'password' => ['required'],
        ]);

        if (Auth::attempt($credentials)) {
            $request->session()->regenerate();
            return redirect()->intended('home');
        }

        return back()->withErrors([
            'email' => 'The provided credentials do not match our records.',
        ]);
    }

    public function logout(Request $request)
    {
        Auth::logout();
        $request->session()->invalidate();
        $request->session()->regenerateToken();
        return redirect('/');
    }
}
```

### Passo 3: Escrever Testes de Autenticação

Escreva os testes para os endpoints de autenticação no arquivo `tests/Feature/AuthenticationTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    public function testLoginUser()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'password',
        ]);

        $response->assertRedirect('/home');
        $this->assertAuthenticatedAs($user);
    }

    public function testLoginFailsWithInvalidCredentials()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'invalid-password',
        ]);

        $response->assertSessionHasErrors('email');
        $this->assertGuest();
    }

    public function testLogoutUser()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $this->actingAs($user)
             ->post('/logout')
             ->assertRedirect('/');

        $this->assertGuest();
    }
}
```

## Testes de Autenticação com Proteção de Rotas

### Implementando Proteção de Rotas

Adicione proteção às rotas que requerem autenticação no arquivo `routes/web.php`:

```php
Route::middleware('auth')->group(function () {
    Route::get('/home', function () {
        return view('home');
    });
});
```

### Teste de Proteção de Rotas

Escreva um teste para verificar se as rotas protegidas requerem autenticação:

```php
public function testProtectedRouteRequiresAuthentication()
{
    $response = $this->get('/home');
    $response->assertRedirect('/login');
}

public function testAuthenticatedUserCanAccessProtectedRoute()
{
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/home');
    $response->assertStatus(200);
}
```

## Práticas Recomendadas

### Testar Cenários de Sucesso e Falha

Garanta que seus testes cubram tanto os cenários de sucesso quanto os de falha, incluindo login, logout e acesso a rotas protegidas.

### Usar Factories para Criar Dados de Teste

Utilize factories para criar usuários e outros dados de teste de forma consistente.

### Verificar Sessões e Tokens

Teste se as sessões e tokens de autenticação são manipulados corretamente durante o login e logout.

## Exemplo Completo

### Controlador LoginController

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class LoginController extends Controller
{
    public function showLoginForm()
    {
        return view('auth.login');
    }

    public function login(Request $request)
    {
        $credentials = $request->validate([
            'email' => ['required', 'email'],
            'password' => ['required'],
        ]);

        if (Auth::attempt($credentials)) {
            $request->session()->regenerate();
            return redirect()->intended('home');
        }

        return back()->withErrors([
            'email' => 'The provided credentials do not match our records.',
        ]);
    }

    public function logout(Request $request)
    {
        Auth::logout();
        $request->session()->invalidate();
        $request->session()->regenerateToken();
        return redirect('/');
    }
}
```

### Teste de Autenticação

No arquivo `tests/Feature/AuthenticationTest.php`, escreva o teste de autenticação:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class AuthenticationTest extends TestCase
{
    use RefreshDatabase;

    public function testLoginUser()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'password',
        ]);

        $response->assertRedirect('/home');
        $this->assertAuthenticatedAs($user);
    }

    public function testLoginFailsWithInvalidCredentials()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $response = $this->post('/login', [
            'email' => 'test@example.com',
            'password' => 'invalid-password',
        ]);

        $response->assertSessionHasErrors('email');
        $this->assertGuest();
    }

    public function testLogoutUser()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
            'password' => bcrypt('password'),
        ]);

        $this->actingAs($user)
             ->post('/logout')
             ->assertRedirect('/');

        $this->assertGuest();
    }

    public function testProtectedRouteRequiresAuthentication()
    {
        $response = $this->get('/home');
        $response->assertRedirect('/login');
    }

    public function testAuthenticatedUserCanAccessProtectedRoute()
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user)->get('/home');
        $response->assertStatus(200);
    }
}
```

## Resumo

Os testes de autenticação são essenciais para garantir que o processo de login, logout e proteção de rotas funcionem conforme o esperado. Com PHPUnit e Pest, você pode escrever testes de autenticação eficientes que garantem a segurança e a confiabilidade da sua aplicação Laravel.
