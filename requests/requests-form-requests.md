# Form Requests

Os Form Requests no Laravel são uma maneira poderosa e organizada de lidar com a validação de dados de requisição. Eles encapsulam a lógica de validação em uma classe separada, tornando o código do controlador mais limpo e fácil de manter.

## Criando Form Requests

### Passo 1: Gerar uma Classe de Form Request

Você pode criar uma classe de Form Request utilizando o comando Artisan:

```bash
php artisan make:request StoreUserRequest
```

### Passo 2: Definir Regras de Validação

Dentro da classe `StoreUserRequest`, defina as regras de validação no método `rules`.

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class StoreUserRequest extends FormRequest
{
    public function authorize()
    {
        return true;
    }

    public function rules()
    {
        return [
            'name' => 'required|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8|confirmed',
        ];
    }

    public function messages()
    {
        return [
            'name.required' => 'O campo nome é obrigatório.',
            'email.required' => 'O campo e-mail é obrigatório.',
            'email.email' => 'Por favor, forneça um endereço de e-mail válido.',
            'email.unique' => 'Este e-mail já está em uso.',
            'password.required' => 'O campo senha é obrigatório.',
            'password.min' => 'A senha deve ter pelo menos 8 caracteres.',
            'password.confirmed' => 'A confirmação da senha não coincide.',
        ];
    }
}
```

### Passo 3: Utilizando Form Requests no Controlador

Injete a classe `StoreUserRequest` no método do controlador onde você deseja validar a requisição.

```php
use App\Http\Requests\StoreUserRequest;

class UserController extends Controller
{
    public function store(StoreUserRequest $request)
    {
        $validated = $request->validated();

        // Processar os dados validados...
        User::create($validated);

        return redirect()->route('users.index');
    }
}
```

## Validação Adicional com Form Requests

### Passo 1: Adicionar Lógica de Validação Adicional

Você pode adicionar lógica de validação adicional no método `withValidator`.

```php
use Illuminate\Contracts\Validation\Validator;
use Illuminate\Validation\ValidationException;

class StoreUserRequest extends FormRequest
{
    // Outros métodos...

    public function withValidator($validator)
    {
        $validator->after(function ($validator) {
            if ($this->somethingElseIsInvalid()) {
                $validator->errors()->add('field', 'Algo mais está inválido!');
            }
        });
    }

    protected function somethingElseIsInvalid()
    {
        // Lógica de validação adicional...
    }
}
```

## Autorização de Form Requests

### Passo 1: Implementar a Lógica de Autorização

Você pode definir a lógica de autorização no método `authorize`.

```php
class StoreUserRequest extends FormRequest
{
    public function authorize()
    {
        // Verificar se o usuário está autorizado a fazer esta requisição
        return auth()->check();
    }

    // Outros métodos...
}
```

## Form Requests como Dependência

### Passo 1: Injetar Form Requests como Dependência

Os Form Requests podem ser injetados como dependência em qualquer lugar onde você precisa deles, não apenas em controladores.

```php
use App\Http\Requests\StoreUserRequest;

class SomeService
{
    public function handle(StoreUserRequest $request)
    {
        $validated = $request->validated();
        
        // Processar os dados validados...
    }
}
```

## Resumo

Os Form Requests no Laravel oferecem uma maneira limpa e organizada de lidar com a validação de dados de requisição. Eles permitem encapsular a lógica de validação e autorização em uma classe separada, tornando o código do controlador mais simples e fácil de manter. Com Form Requests, você pode adicionar validação adicional, personalizar mensagens de erro e usar validação condicional, tudo em um só lugar.
