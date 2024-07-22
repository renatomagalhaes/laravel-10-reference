# Testes Unitários

Os testes unitários são usados para verificar unidades individuais de código, como funções ou métodos, isolando-os do resto da aplicação. Eles são rápidos de executar e fornecem feedback imediato sobre a funcionalidade do código.

## Importância dos Testes Unitários

### Benefícios

- **Qualidade do Código:** Garantem que cada parte do código funcione conforme o esperado.
- **Feedback Rápido:** Fornecem feedback imediato sobre alterações no código.
- **Facilidade de Manutenção:** Facilitam a detecção de regressões ao modificar o código existente.
- **Documentação:** Servem como documentação do comportamento esperado do código.

## Estrutura Básica de um Teste Unitário

### Exemplo com PHPUnit

Crie um teste unitário em `tests/Unit/ExampleTest.php`:

```php
namespace Tests\Unit;

use PHPUnit\Framework\TestCase;

class ExampleTest extends TestCase
{
    public function testBasicTest()
    {
        $this->assertTrue(true);
    }
}
```

### Exemplo com Pest

Crie um teste unitário com Pest:

```php
it('is true', function () {
    expect(true)->toBeTrue();
});
```

## Criando Testes Unitários

### Passo 1: Criar um Teste Unitário

Use o comando Artisan para criar um teste unitário:

```bash
php artisan make:test UserTest --unit
```

### Passo 2: Escrever o Teste

No arquivo `tests/Unit/UserTest.php`, escreva um teste para verificar a funcionalidade da classe `User`:

```php
namespace Tests\Unit;

use App\Models\User;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    public function testFullName()
    {
        $user = new User();
        $user->first_name = 'John';
        $user->last_name = 'Doe';

        $this->assertEquals('John Doe', $user->fullName());
    }
}
```

### Passo 3: Executar o Teste

Para executar o teste, use o PHPUnit:

```bash
vendor/bin/phpunit --filter UserTest
```

Ou use o Pest:

```bash
./vendor/bin/pest --filter UserTest
```

## Estruturando Testes Unitários

### Configuração e Limpeza

Use os métodos `setUp` e `tearDown` para configurar e limpar o ambiente de teste:

```php
namespace Tests\Unit;

use App\Models\User;
use PHPUnit\Framework\TestCase;

class UserTest extends TestCase
{
    protected $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user = new User();
    }

    protected function tearDown(): void
    {
        unset($this->user);
        parent::tearDown();
    }

    public function testFullName()
    {
        $this->user->first_name = 'John';
        $this->user->last_name = 'Doe';

        $this->assertEquals('John Doe', $this->user->fullName());
    }
}
```

### Testando Exceções

Use o método `expectException` para testar se uma exceção é lançada:

```php
namespace Tests\Unit;

use App\Models\User;
use PHPUnit\Framework\TestCase;
use InvalidArgumentException;

class UserTest extends TestCase
{
    public function testInvalidEmail()
    {
        $this->expectException(InvalidArgumentException::class);

        $user = new User();
        $user->email = 'invalid-email';
    }
}
```

## Práticas Recomendadas

### Escrever Testes Pequenos e Focados

Cada teste deve verificar uma única funcionalidade ou comportamento. Isso facilita a manutenção e a compreensão dos testes.

### Nomear Testes Descritivamente

Use nomes descritivos para os métodos de teste que indiquem claramente o que está sendo testado e o resultado esperado.

### Manter Testes Independentes

Cada teste deve ser independente dos outros. A ordem de execução dos testes não deve afetar os resultados.

## Exemplo Completo

### Modelo User

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use InvalidArgumentException;

class User extends Model
{
    public $first_name;
    public $last_name;
    public $email;

    public function fullName()
    {
        return "{$this->first_name} {$this->last_name}";
    }

    public function setEmailAttribute($value)
    {
        if (!filter_var($value, FILTER_VALIDATE_EMAIL)) {
            throw new InvalidArgumentException("Invalid email address");
        }
        $this->attributes['email'] = $value;
    }
}
```

### Teste Unitário para User

```php
namespace Tests\Unit;

use App\Models\User;
use PHPUnit\Framework\TestCase;
use InvalidArgumentException;

class UserTest extends TestCase
{
    protected $user;

    protected function setUp(): void
    {
        parent::setUp();
        $this->user = new User();
    }

    protected function tearDown(): void
    {
        unset($this->user);
        parent::tearDown();
    }

    public function testFullName()
    {
        $this->user->first_name = 'John';
        $this->user->last_name = 'Doe';

        $this->assertEquals('John Doe', $this->user->fullName());
    }

    public function testInvalidEmail()
    {
        $this->expectException(InvalidArgumentException::class);
        $this->user->email = 'invalid-email';
    }
}
```

## Resumo

Os testes unitários são fundamentais para garantir a qualidade do seu código. Eles verificam a funcionalidade de unidades individuais de código, fornecendo feedback rápido e facilitando a manutenção e refatoração do código. Com PHPUnit e Pest, você pode escrever testes unitários de forma eficiente e eficaz no Laravel.
