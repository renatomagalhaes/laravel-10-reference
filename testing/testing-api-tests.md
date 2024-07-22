# Testes de API

Os testes de API garantem que os endpoints da API funcionem conforme o esperado, verificando respostas, validações, autenticação e autorização. Eles são essenciais para manter a integridade e a confiabilidade de uma aplicação que expõe APIs.

## Importância dos Testes de API

### Benefícios

- **Confiabilidade:** Garantem que os endpoints da API funcionem corretamente e retornem as respostas esperadas.
- **Segurança:** Verificam se a autenticação e a autorização estão implementadas corretamente.
- **Validação:** Asseguram que os dados enviados e recebidos estejam validados conforme as regras de negócio.
- **Documentação:** Servem como documentação para o comportamento esperado dos endpoints.

## Estrutura Básica de um Teste de API

### Exemplo de Teste de API

Crie um teste de API para verificar o comportamento de um endpoint de criação de usuário.

#### Exemplo com PHPUnit

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserApiTest extends TestCase
{
    use RefreshDatabase;

    public function testCreateUser()
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

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

### Exemplo com Pest

```php
it('creates a user', function () {
    $response = $this->postJson('/api/users', [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);

    $response->assertStatus(201)
             ->assertJson(['created' => true]);

    expect('users')->toHaveRecord(['email' => 'john@example.com']);
});
```

## Criando Testes de API

### Passo 1: Configurar o Ambiente de Testes

Certifique-se de que o ambiente de testes esteja configurado corretamente, incluindo o banco de dados de testes e a configuração das variáveis de ambiente no arquivo `.env.testing`.

### Passo 2: Criar Endpoints de API

Defina os endpoints de API no arquivo `routes/api.php`:

```php
use App\Http\Controllers\Api\UserController;

Route::post('/users', [UserController::class, 'store']);
```

### Passo 3: Implementar o Controlador

Implemente o controlador `UserController` para lidar com a criação de usuários:

```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8',
        ]);

        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        return response()->json(['created' => true], 201);
    }
}
```

### Passo 4: Escrever Testes de API

Escreva os testes para os endpoints de API no arquivo `tests/Feature/UserApiTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserApiTest extends TestCase
{
    use RefreshDatabase;

    public function testCreateUser()
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

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }

    public function testValidationError()
    {
        $response = $this->json('POST', '/api/users', [
            'name' => '',
            'email' => 'not-an-email',
            'password' => 'short',
        ]);

        $response->assertStatus(422)
                 ->assertJsonStructure([
                     'name', 'email', 'password'
                 ]);
    }
}
```

## Testes de API com Autenticação

### Implementando Autenticação

Adicione autenticação aos seus endpoints de API.

```php
Route::middleware('auth:sanctum')->group(function () {
    Route::post('/users', [UserController::class, 'store']);
});
```

### Teste de Autenticação

Escreva um teste para verificar a autenticação:

```php
public function testAuthenticationRequired()
{
    $response = $this->json('POST', '/api/users', [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);

    $response->assertStatus(401);
}
```

### Teste de API com Usuário Autenticado

```php
public function testCreateUserAuthenticated()
{
    $user = User::factory()->create();

    $response = $this->actingAs($user, 'sanctum')->json('POST', '/api/users', [
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);

    $response->assertStatus(201)
             ->assertJson(['created' => true]);

    $this->assertDatabaseHas('users', [
        'email' => 'john@example.com',
    ]);
}
```

## Práticas Recomendadas

### Testar Cenários Positivos e Negativos

Garanta que seus testes cubram tanto os cenários de sucesso quanto os de erro.

### Usar Factories para Criar Dados de Teste

Utilize factories para criar dados de teste realistas e consistentes.

### Verificar Estrutura de Respostas

Teste a estrutura das respostas JSON para garantir que elas contenham os campos esperados.

## Exemplo Completo

### Controlador UserController

```php
namespace App\Http\Controllers\Api;

use App\Http\Controllers\Controller;
use App\Models\User;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8',
        ]);

        if ($validator->fails()) {
            return response()->json($validator->errors(), 422);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        return response()->json(['created' => true], 201);
    }
}
```

### Teste de API

No arquivo `tests/Feature/UserApiTest.php`, escreva o teste de API:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserApiTest extends TestCase
{
    use RefreshDatabase;

    public function testCreateUser()
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

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }

    public function testValidationError()
    {
        $response = $this->json('POST', '/api/users', [
            'name' => '',
            'email' => 'not-an-email',
            'password' => 'short',
        ]);

        $response->assertStatus(422)
                 ->assertJsonStructure([
                     'name', 'email', 'password'
                 ]);
    }

    public function testAuthenticationRequired()
    {
        $response = $this->json('POST', '/api/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $response->assertStatus(401);
    }

    public function testCreateUserAuthenticated()
    {
        $user = User::factory()->create();

        $response = $this->actingAs($user, 'sanctum')->json('POST', '/api/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $response->assertStatus(201)
                 ->assertJson(['created' => true]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Resumo

Os testes de API são essenciais para garantir que os endpoints funcionem conforme o esperado, validando respostas, autenticação, autorização e integridade dos dados. Com PHPUnit e Pest, você pode escrever testes de API eficientes que garantem a confiabilidade e a segurança dos seus endpoints na aplicação Laravel.
