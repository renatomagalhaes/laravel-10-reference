# Testes de Integração

Os testes de integração garantem que diferentes módulos ou componentes da aplicação funcionem corretamente em conjunto. Eles verificam a interação entre várias partes do sistema, como controladores, modelos, serviços e bibliotecas externas.

## Importância dos Testes de Integração

### Benefícios

- **Verificação da Interação:** Assegura que diferentes partes do sistema funcionem bem juntas.
- **Detecção de Problemas de Integração:** Identifica problemas que podem surgir quando componentes individuais são combinados.
- **Confiança no Sistema:** Fornece confiança de que a aplicação como um todo funciona conforme o esperado.

## Estrutura Básica de um Teste de Integração

### Exemplo de Teste de Integração

Crie um teste para verificar o comportamento de integração entre o controlador e o modelo de usuários.

#### Exemplo com PHPUnit

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;
use App\Services\UserService;

class IntegrationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserServiceCreatesUser()
    {
        $userService = new UserService();
        $user = $userService->createUser([
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);

        $this->assertEquals('John Doe', $user->name);
    }
}
```

### Exemplo com Pest

```php
use App\Models\User;
use App\Services\UserService;

it('creates a user through the user service', function () {
    $userService = new UserService();
    $user = $userService->createUser([
        'name' => 'John Doe',
        'email' => 'john@example.com',
        'password' => 'password',
    ]);

    expect('users')->toHaveRecord([
        'email' => 'john@example.com',
    ]);

    expect($user->name)->toBe('John Doe');
});
```

## Criando Testes de Integração

### Passo 1: Configurar Serviços

Crie um serviço para lidar com a lógica de criação de usuários em `app/Services/UserService.php`:

```php
namespace App\Services;

use App\Models\User;
use Illuminate\Support\Facades\Hash;

class UserService
{
    public function createUser(array $data)
    {
        return User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
        ]);
    }
}
```

### Passo 2: Implementar Controladores

Implemente o controlador `UserController` para lidar com requisições de usuários em `app/Http/Controllers/UserController.php`:

```php
namespace App\Http\Controllers;

use App\Services\UserService;
use Illuminate\Http\Request;

class UserController extends Controller
{
    protected $userService;

    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function store(Request $request)
    {
        $user = $this->userService->createUser($request->all());

        return response()->json($user, 201);
    }
}
```

### Passo 3: Escrever Testes de Integração

Escreva os testes para verificar a integração entre o controlador e o serviço em `tests/Feature/IntegrationTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Services\UserService;

class IntegrationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserServiceCreatesUser()
    {
        $userService = new UserService();
        $user = $userService->createUser([
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);

        $this->assertEquals('John Doe', $user->name);
    }

    public function testUserControllerStoresUser()
    {
        $response = $this->postJson('/api/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $response->assertStatus(201)
                 ->assertJson(['email' => 'john@example.com']);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Práticas Recomendadas

### Testar Cenários de Sucesso e Falha

Garanta que seus testes de integração cubram tanto os cenários de sucesso quanto os de falha, incluindo validação de dados e tratamento de erros.

### Usar Factories para Criar Dados de Teste

Utilize factories para criar usuários e outros dados de teste de forma consistente.

### Verificar Interações entre Componentes

Teste as interações entre diferentes componentes da aplicação para garantir que funcionem corretamente quando combinados.

## Exemplo Completo

### Serviço UserService

```php
namespace App\Services;

use App\Models\User;
use Illuminate\Support\Facades\Hash;

class UserService
{
    public function createUser(array $data)
    {
        return User::create([
            'name' => $data['name'],
            'email' => $data['email'],
            'password' => Hash::make($data['password']),
        ]);
    }
}
```

### Controlador UserController

```php
namespace App\Http\Controllers;

use App\Services\UserService;
use Illuminate\Http\Request;

class UserController extends Controller
{
    protected $userService;

    public function __construct(UserService $userService)
    {
        $this->userService = $userService;
    }

    public function store(Request $request)
    {
        $user = $this->userService->createUser($request->all());

        return response()->json($user, 201);
    }
}
```

### Teste de Integração

No arquivo `tests/Feature/IntegrationTest.php`, escreva o teste de integração:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Services\UserService;

class IntegrationTest extends TestCase
{
    use RefreshDatabase;

    public function testUserServiceCreatesUser()
    {
        $userService = new UserService();
        $user = $userService->createUser([
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);

        $this->assertEquals('John Doe', $user->name);
    }

    public function testUserControllerStoresUser()
    {
        $response = $this->postJson('/api/users', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
        ]);

        $response->assertStatus(201)
                 ->assertJson(['email' => 'john@example.com']);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Resumo

Os testes de integração são essenciais para garantir que diferentes módulos ou componentes da aplicação funcionem corretamente em conjunto. Com PHPUnit e Pest, você pode escrever testes de integração eficientes que verificam a interação entre várias partes do sistema e garantem a confiabilidade e a integridade da sua aplicação Laravel.
