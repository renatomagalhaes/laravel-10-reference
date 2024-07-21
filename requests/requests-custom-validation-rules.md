# Custom Validation Rules

No Laravel, além das regras de validação embutidas, você pode criar regras de validação personalizadas para atender às necessidades específicas da sua aplicação. Essas regras permitem que você valide dados de maneira mais detalhada e específica.

## Criando Regras de Validação Personalizadas

### Passo 1: Criar uma Regra de Validação

Você pode criar uma regra de validação personalizada usando o comando Artisan `make:rule`.

```bash
php artisan make:rule Uppercase
```

### Passo 2: Implementar a Regra de Validação

Dentro da classe da regra de validação, implemente a lógica para validar o campo.

```php
namespace App\Rules;

use Illuminate\Contracts\Validation\Rule;

class Uppercase implements Rule
{
    public function passes($attribute, $value)
    {
        return strtoupper($value) === $value;
    }

    public function message()
    {
        return 'The :attribute must be uppercase.';
    }
}
```

### Passo 3: Utilizar a Regra de Validação

Você pode usar a regra de validação personalizada em seus controladores ou Form Requests.

```php
use App\Rules\Uppercase;

Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'name' => ['required', 'max:255', new Uppercase],
    ]);

    // Processar os dados validados...
});
```

## Mensagens de Erro Personalizadas

### Passo 1: Definir Mensagens de Erro Personalizadas no Controlador

Você pode personalizar a mensagem de erro diretamente no controlador ao usar a regra personalizada.

```php
Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'name' => ['required', 'max:255', new Uppercase],
    ], [
        'name.uppercase' => 'O nome deve estar em letras maiúsculas.',
    ]);

    // Processar os dados validados...
});
```

### Passo 2: Definir Mensagens de Erro Personalizadas em Form Requests

Você pode definir mensagens de erro personalizadas em classes de Form Request.

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;
use App\Rules\Uppercase;

class StoreUserRequest extends FormRequest
{
    public function rules()
    {
        return [
            'name' => ['required', 'max:255', new Uppercase],
        ];
    }

    public function messages()
    {
        return [
            'name.uppercase' => 'O nome deve estar em letras maiúsculas.',
        ];
    }
}
```

## Validações Atípicas

### Validação de Arrays

Você pode criar regras de validação para arrays complexos.

```php
Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'contacts.*.email' => 'email',
        'contacts.*.phone' => 'required',
    ]);

    // Processar os dados validados...
});
```

### Validação Condicional

Você pode aplicar validações condicionais baseadas em outros campos da requisição.

```php
Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'password' => 'required_if:account_type,admin',
    ]);

    // Processar os dados validados...
});
```

## Resumo

As regras de validação personalizadas no Laravel permitem que você adicione lógica de validação específica para sua aplicação, além das regras de validação padrão. Criar e usar regras de validação personalizadas é simples e proporciona um nível mais alto de controle sobre a validação dos dados da sua aplicação. Com essas ferramentas, você pode garantir que os dados recebidos sejam válidos e atendam aos critérios específicos definidos pela lógica de negócios.
