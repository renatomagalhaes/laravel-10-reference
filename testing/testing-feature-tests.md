# Testes de Feature

Os testes de feature verificam funcionalidades inteiras da aplicação, incluindo a interação entre várias partes do sistema. Eles são mais lentos que os testes unitários, mas fornecem uma visão abrangente do comportamento da aplicação.

## Importância dos Testes de Feature

### Benefícios

- **Verificação de Funcionalidades Completas:** Garantem que diferentes componentes do sistema funcionem bem juntos.
- **Cobertura Abrangente:** Testam o fluxo completo de uma funcionalidade, incluindo validação, interação com o banco de dados e resposta do sistema.
- **Confiança no Código:** Fornecem confiança de que as funcionalidades principais da aplicação estão funcionando conforme o esperado.

## Estrutura Básica de um Teste de Feature

### Exemplo com PHPUnit

Crie um teste de feature em `tests/Feature/ExampleTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class ExampleTest extends TestCase
{
    use RefreshDatabase;

    public function testHomePage()
    {
        $response = $this->get('/');

        $response->assertStatus(200);
    }
}
```

### Exemplo com Pest

Crie um teste de feature com Pest:

```php
it('has home page', function () {
    $response = $this->get('/');

    $response->assertStatus(200);
});
```

## Criando Testes de Feature

### Passo 1: Criar um Teste de Feature

Use o comando Artisan para criar um teste de feature:

```bash
php artisan make:test UserFeatureTest
```

### Passo 2: Escrever o Teste

No arquivo `tests/Feature/UserFeatureTest.php`, escreva um teste para verificar a funcionalidade de registro de usuários:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserFeatureTest extends TestCase
{
    use RefreshDatabase;

    public function testUserRegistration()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertStatus(302);
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

### Passo 3: Executar o Teste

Para executar o teste, use o PHPUnit:

```bash
vendor/bin/phpunit --filter UserFeatureTest
```

Ou use o Pest:

```bash
./vendor/bin/pest --filter UserFeatureTest
```

## Estruturando Testes de Feature

### Configuração e Limpeza

Use o trait `RefreshDatabase` para garantir que o banco de dados esteja em um estado limpo antes de cada teste:

```php
use Illuminate\Foundation\Testing\RefreshDatabase;

class UserFeatureTest extends TestCase
{
    use RefreshDatabase;

    // Testes aqui
}
```

### Testando Rotas Protegidas

Use a autenticação para testar rotas protegidas:

```php
public function testProtectedRoute()
{
    $user = User::factory()->create();

    $response = $this->actingAs($user)->get('/dashboard');

    $response->assertStatus(200);
}
```

### Testando APIs

Para testar APIs, use o método `json` para enviar requisições e verificar as respostas:

```php
public function testApiEndpoint()
{
    $response = $this->json('POST', '/api/users', [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);

    $response->assertStatus(201)
             ->assertJson([
                 'created' => true,
             ]);
}
```

## Práticas Recomendadas

### Escrever Testes Abrangentes

Garanta que seus testes de feature cubram todos os cenários possíveis, incluindo casos de sucesso e falha.

### Manter Testes Rápidos

Embora os testes de feature sejam mais lentos que os unitários, tente mantê-los o mais rápido possível, evitando operações desnecessárias.

### Usar Dados Falsos Realistas

Use factories e seeders para gerar dados de teste realistas, garantindo que os testes sejam o mais próximo possível do ambiente de produção.

## Exemplo Completo

### Rota de Registro

No arquivo `routes/web.php`, defina uma rota para registro de usuários:

```php
use App\Http\Controllers\Auth\RegisterController;

Route::post('/register', [RegisterController::class, 'register']);
```

### Controlador de Registro

No arquivo `app/Http/Controllers/Auth/RegisterController.php`, defina o método `register`:

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;

class RegisterController extends Controller
{
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        if ($validator->fails()) {
            return redirect('/register')
                        ->withErrors($validator)
                        ->withInput();
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        auth()->login($user);

        return redirect('/dashboard');
    }
}
```

### Teste de Feature para Registro

No arquivo `tests/Feature/UserFeatureTest.php`, escreva o teste de feature:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserFeatureTest extends TestCase
{
    use RefreshDatabase;

    public function testUserRegistration()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertStatus(302);
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Resumo

Os testes de feature são essenciais para garantir que as funcionalidades completas da aplicação funcionem conforme o esperado. Eles verificam a interação entre diferentes componentes do sistema, proporcionando uma cobertura abrangente e aumentando a confiança no código. Com PHPUnit e Pest, você pode escrever testes de feature de forma eficiente no Laravel.
