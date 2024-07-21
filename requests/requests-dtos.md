# Data Transfer Objects (DTOs)

Data Transfer Objects (DTOs) são objetos usados para transportar dados entre diferentes camadas de uma aplicação. Eles ajudam a manter a aplicação organizada e a reduzir a dependência entre camadas, especialmente quando há necessidade de validações complexas e transformações de dados.

## Introdução aos DTOs

### O que são DTOs?

DTOs são objetos simples que não possuem lógica de negócios. Eles são usados para encapsular dados e transportá-los entre diferentes partes de uma aplicação. DTOs podem ser úteis para:

- Simplificar a interface entre camadas.
- Melhorar a legibilidade e manutenção do código.
- Realizar validações e transformações de dados.

### Exemplo de DTO Básico

Vamos criar um DTO simples para encapsular dados de um usuário:

```php
namespace App\DTOs;

class UserDTO
{
    public $name;
    public $email;

    public function __construct($name, $email)
    {
        $this->name = $name;
        $this->email = $email;
    }
}
```

## Uso de DTOs em Requisições

### Passo 1: Criar o DTO

Crie um DTO para encapsular os dados da requisição.

```php
namespace App\DTOs;

class CreateUserDTO
{
    public $name;
    public $email;
    public $password;

    public function __construct($name, $email, $password)
    {
        $this->name = $name;
        $this->email = $email;
        $this->password = $password;
    }
}
```

### Passo 2: Usar o DTO no Controlador

Injete o DTO no controlador e use-o para transportar dados entre as camadas.

```php
use App\DTOs\CreateUserDTO;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $validatedData = $request->validate([
            'name' => 'required|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8|confirmed',
        ]);

        $dto = new CreateUserDTO(
            $validatedData['name'],
            $validatedData['email'],
            $validatedData['password']
        );

        // Use o DTO para criar o usuário
        $user = User::create([
            'name' => $dto->name,
            'email' => $dto->email,
            'password' => bcrypt($dto->password),
        ]);

        return redirect()->route('users.index');
    }
}
```

## Validação e Transformação com DTOs

### Passo 1: Adicionar Validações no DTO

Você pode adicionar validações no DTO para garantir que os dados estejam corretos antes de processá-los.

```php
namespace App\DTOs;

use Illuminate\Support\Facades\Validator;
use Illuminate\Validation\ValidationException;

class CreateUserDTO
{
    public $name;
    public $email;
    public $password;

    public function __construct($name, $email, $password)
    {
        $this->name = $name;
        $this->email = $email;
        $this->password = $password;
    }

    public static function fromRequest($request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|max:255',
            'email' => 'required|email|unique:users',
            'password' => 'required|min:8|confirmed',
        ]);

        if ($validator->fails()) {
            throw new ValidationException($validator);
        }

        return new self(
            $request->input('name'),
            $request->input('email'),
            $request->input('password')
        );
    }
}
```

### Passo 2: Usar o Método de Criação no Controlador

Use o método `fromRequest` no controlador para criar o DTO a partir da requisição.

```php
use App\DTOs\CreateUserDTO;

class UserController extends Controller
{
    public function store(Request $request)
    {
        $dto = CreateUserDTO::fromRequest($request);

        // Use o DTO para criar o usuário
        $user = User::create([
            'name' => $dto->name,
            'email' => $dto->email,
            'password' => bcrypt($dto->password),
        ]);

        return redirect()->route('users.index');
    }
}
```

## Resumo

Os Data Transfer Objects (DTOs) são ferramentas úteis para encapsular dados e transportá-los entre diferentes partes de uma aplicação. Eles ajudam a manter o código organizado, facilitam a validação e transformação de dados e reduzem a dependência entre camadas. Utilizando DTOs no Laravel, você pode melhorar a estrutura e a manutenção da sua aplicação.
