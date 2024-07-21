# Validação de Requisições

A validação de dados é um aspecto crucial em qualquer aplicação web para garantir que os dados fornecidos pelos usuários atendam aos critérios esperados antes de serem processados ou armazenados. O Laravel oferece uma maneira poderosa e flexível de validar requisições de forma simples e eficiente.

## Validação Básica

### Validação no Controlador

Você pode validar dados diretamente no controlador utilizando o método `validate` do objeto `Request`.

### Exemplo de Validação no Controlador

```php
use Illuminate\Http\Request;

Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'name' => 'required|max:255',
        'email' => 'required|email',
        'age' => 'nullable|integer|min:18',
    ]);

    // Processar os dados validados...
});
```

### Mensagens de Erro Personalizadas

Você pode personalizar as mensagens de erro adicionando um array de mensagens ao método `validate`.

### Exemplo de Mensagens de Erro Personalizadas

```php
Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'name' => 'required|max:255',
        'email' => 'required|email',
    ], [
        'name.required' => 'O campo nome é obrigatório.',
        'email.required' => 'O campo e-mail é obrigatório.',
        'email.email' => 'Por favor, forneça um endereço de e-mail válido.',
    ]);

    // Processar os dados validados...
});
```

## Regras de Validação Personalizadas

### Criando Regras Personalizadas

Você pode criar regras de validação personalizadas utilizando a classe `Rule`.

### Exemplo de Regra de Validação Personalizada

```php
use Illuminate\Validation\Rule;

Route::post('/profile', function (Request $request) {
    $validatedData = $request->validate([
        'username' => [
            'required',
            Rule::unique('users')->ignore($request->user()->id),
        ],
    ]);

    // Processar os dados validados...
});
```

### Objeto Validator

O objeto `Validator` oferece uma maneira mais detalhada de validar dados, permitindo a execução de lógica complexa antes da validação.

### Exemplo de Uso do Objeto Validator

```php
use Illuminate\Support\Facades\Validator;

Route::post('/profile', function (Request $request) {
    $validator = Validator::make($request->all(), [
        'name' => 'required|max:255',
        'email' => 'required|email',
    ]);

    if ($validator->fails()) {
        return redirect('/profile')
                    ->withErrors($validator)
                    ->withInput();
    }

    // Processar os dados validados...
});
```

## Form Requests

### Criando Form Requests

Form Requests encapsulam a lógica de validação em uma classe separada, tornando o código mais limpo e organizado.

### Exemplo de Criação de Form Request

```bash
php artisan make:request UpdateProfileRequest
```

### Implementando Validação em Form Requests

Defina as regras de validação no método `rules` da classe `FormRequest`.

```php
namespace App\Http\Requests;

use Illuminate\Foundation\Http\FormRequest;

class UpdateProfileRequest extends FormRequest
{
    public function rules()
    {
        return [
            'name' => 'required|max:255',
            'email' => 'required|email',
        ];
    }

    public function messages()
    {
        return [
            'name.required' => 'O campo nome é obrigatório.',
            'email.required' => 'O campo e-mail é obrigatório.',
            'email.email' => 'Por favor, forneça um endereço de e-mail válido.',
        ];
    }
}
```

### Utilizando Form Requests no Controlador

Injete a classe `FormRequest` no método do controlador.

```php
use App\Http\Requests\UpdateProfileRequest;

Route::post('/profile', function (UpdateProfileRequest $request) {
    // Processar os dados validados...
});
```

## Resumo

A validação de requisições no Laravel é poderosa e flexível, permitindo a validação diretamente no controlador, o uso de regras personalizadas e a encapsulação de lógica de validação em Form Requests. A personalização das mensagens de erro e o uso do objeto `Validator` oferecem um controle detalhado sobre o processo de validação, garantindo que os dados recebidos atendam aos critérios necessários antes de serem processados.
