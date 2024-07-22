# Testes de Validação

A validação é uma parte crucial de qualquer aplicação, garantindo que os dados recebidos estejam corretos antes de serem processados. Testar a validação ajuda a garantir que as regras de negócio sejam aplicadas corretamente e que os dados inválidos sejam tratados apropriadamente.

## Importância dos Testes de Validação

### Benefícios

- **Segurança:** Evita que dados inválidos ou maliciosos sejam processados pela aplicação.
- **Integridade dos Dados:** Assegura que apenas dados válidos sejam armazenados no banco de dados.
- **Confiabilidade:** Garante que as regras de negócio sejam aplicadas de forma consistente.

## Estrutura Básica de um Teste de Validação

### Exemplo de Validação

Crie um teste de validação para verificar o comportamento do controlador de registro de usuários.

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Illuminate\Foundation\Testing\WithFaker;
use Tests\TestCase;

class RegistrationTest extends TestCase
{
    use RefreshDatabase, WithFaker;

    public function testValidatesRequiredFields()
    {
        $response = $this->post('/register', []);

        $response->assertSessionHasErrors(['name', 'email', 'password']);
    }

    public function testValidatesEmailFormat()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'invalid-email',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertSessionHasErrors(['email']);
    }

    public function testRegistersUserWithValidData()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertRedirect('/home');
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Validando Regras Customizadas

### Exemplo de Regra Customizada

Crie uma regra customizada para validar que o nome do usuário não contenha números.

#### Arquivo da Regra

```php
namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class NoNumbers implements Rule
{
    public function passes($attribute, $value)
    {
        return !preg_match('/[0-9]/', $value);
    }

    public function message()
    {
        return 'The :attribute cannot contain numbers.';
    }
}
```

### Usando a Regra no Formulário

Adicione a regra customizada ao controlador de registro.

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Rules\NoNumbers;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Validator;

class RegisterController extends Controller
{
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => ['required', 'string', 'max:255', new NoNumbers],
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        if ($validator->fails()) {
            return redirect('/register')
                        ->withErrors($validator)
                        ->withInput();
        }

        // Lógica de registro do usuário
    }
}
```

### Testando a Regra Customizada

Adicione testes para verificar a regra customizada.

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class RegistrationTest extends TestCase
{
    use RefreshDatabase;

    public function testValidatesNoNumbersInName()
    {
        $response = $this->post('/register', [
            'name' => 'John123',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertSessionHasErrors(['name']);
    }
}
```

## Práticas Recomendadas

### Usar Mensagens de Erro Personalizadas

Forneça mensagens de erro personalizadas e claras para ajudar os usuários a corrigirem seus erros.

### Testar Cenários Positivos e Negativos

Teste tanto os cenários em que a validação deve falhar quanto os em que deve passar, garantindo que todos os casos possíveis sejam cobertos.

### Manter Validações Consistentes

Certifique-se de que as validações sejam consistentes em toda a aplicação, evitando regras conflitantes ou inconsistentes.

## Exemplo Completo

### Controlador de Registro

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use App\Models\User;
use App\Rules\NoNumbers;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;

class RegisterController extends Controller
{
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => ['required', 'string', 'max:255', new NoNumbers],
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

        return redirect('/home');
    }
}
```

### Teste de Validação

No arquivo `tests/Feature/RegistrationTest.php`, escreva o teste de validação:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class RegistrationTest extends TestCase
{
    use RefreshDatabase;

    public function testValidatesRequiredFields()
    {
        $response = $this->post('/register', []);

        $response->assertSessionHasErrors(['name', 'email', 'password']);
    }

    public function testValidatesEmailFormat()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'invalid-email',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertSessionHasErrors(['email']);
    }

    public function testRegistersUserWithValidData()
    {
        $response = $this->post('/register', [
            'name' => 'John Doe',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertRedirect('/home');
        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }

    public function testValidatesNoNumbersInName()
    {
        $response = $this->post('/register', [
            'name' => 'John123',
            'email' => 'john@example.com',
            'password' => 'password',
            'password_confirmation' => 'password',
        ]);

        $response->assertSessionHasErrors(['name']);
    }
}
```

## Resumo

Testar validações é fundamental para garantir que os dados recebidos pela aplicação sejam válidos e seguros. Com PHPUnit e Pest, você pode escrever testes de validação eficientes que garantem a integridade e a confiabilidade dos dados na sua aplicação Laravel.
