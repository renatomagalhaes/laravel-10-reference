# Testes de Banco de Dados

Os testes de banco de dados são essenciais para garantir que as interações com o banco de dados funcionem conforme o esperado. Eles verificam operações de leitura, escrita, atualização e exclusão de dados, além de garantir a integridade dos dados.

## Importância dos Testes de Banco de Dados

### Benefícios

- **Verificação de Operações CRUD:** Garantem que as operações de criação, leitura, atualização e exclusão funcionem corretamente.
- **Integridade dos Dados:** Asseguram que os dados no banco de dados permaneçam consistentes e íntegros.
- **Confiança no Código:** Fornecem confiança de que as interações com o banco de dados estão funcionando conforme o esperado.

## Estrutura Básica de um Teste de Banco de Dados

### Exemplo com PHPUnit

Crie um teste de banco de dados em `tests/Feature/DatabaseTest.php`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class DatabaseTest extends TestCase
{
    use RefreshDatabase;

    public function testDatabaseHasUser()
    {
        User::factory()->create([
            'email' => 'john@example.com',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

### Exemplo com Pest

Crie um teste de banco de dados com Pest:

```php
it('has user in database', function () {
    User::factory()->create(['email' => 'john@example.com']);

    expect('users')->toHaveRecord(['email' => 'john@example.com']);
});
```

## Configurando Testes de Banco de Dados

### Passo 1: Configurar o Banco de Dados de Testes

No arquivo `.env.testing`, configure as variáveis de ambiente do banco de dados de testes:

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=test_database
DB_USERNAME=root
DB_PASSWORD=password
```

### Passo 2: Migrar o Banco de Dados de Testes

Antes de executar testes que dependem do banco de dados, certifique-se de migrar o banco de dados de testes:

```bash
php artisan migrate --env=testing
```

### Passo 3: Criar Factories para Dados de Teste

Use factories para criar dados de teste realistas. No arquivo `database/factories/UserFactory.php`:

```php
namespace Database\Factories;

use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    protected $model = User::class;

    public function definition()
    {
        return [
            'name' => $this->faker->name,
            'email' => $this->faker->unique()->safeEmail,
            'email_verified_at' => now(),
            'password' => bcrypt('password'), // password
            'remember_token' => Str::random(10),
        ];
    }
}
```

## Exemplos de Testes de Banco de Dados

### Teste de Criação de Registro

```php
public function testCreateUser()
{
    $user = User::factory()->create([
        'name' => 'John Doe',
        'email' => 'john@example.com',
    ]);

    $this->assertDatabaseHas('users', [
        'email' => 'john@example.com',
    ]);
}
```

### Teste de Atualização de Registro

```php
public function testUpdateUser()
{
    $user = User::factory()->create([
        'name' => 'John Doe',
        'email' => 'john@example.com',
    ]);

    $user->update(['name' => 'Jane Doe']);

    $this->assertDatabaseHas('users', [
        'email' => 'john@example.com',
        'name' => 'Jane Doe',
    ]);
}
```

### Teste de Exclusão de Registro

```php
public function testDeleteUser()
{
    $user = User::factory()->create([
        'email' => 'john@example.com',
    ]);

    $user->delete();

    $this->assertDatabaseMissing('users', [
        'email' => 'john@example.com',
    ]);
}
```

## Testes de Transações

Para garantir que as transações do banco de dados funcionem conforme o esperado, você pode usar o método `transaction`:

```php
use Illuminate\Support\Facades\DB;

public function testDatabaseTransaction()
{
    DB::transaction(function () {
        $user = User::factory()->create([
            'email' => 'john@example.com',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    });

    $this->assertDatabaseHas('users', [
        'email' => 'john@example.com',
    ]);
}
```

## Práticas Recomendadas

### Usar RefreshDatabase

Use o trait `RefreshDatabase` para garantir que o banco de dados seja limpo antes de cada teste, proporcionando um estado consistente para cada execução de teste.

### Isolar Testes de Banco de Dados

Cada teste deve ser isolado para garantir que a execução de um teste não afete o resultado de outro.

### Usar Factories

Use factories para gerar dados de teste realistas, facilitando a criação e manipulação de registros no banco de dados.

## Exemplo Completo

### Modelo User

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable
{
    use HasFactory;

    protected $fillable = [
        'name', 'email', 'password',
    ];

    protected $hidden = [
        'password', 'remember_token',
    ];

    protected $casts = [
        'email_verified_at' => 'datetime',
    ];
}
```

### Teste de Banco de Dados

No arquivo `tests/Feature/UserDatabaseTest.php`, escreva o teste de banco de dados:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class UserDatabaseTest extends TestCase
{
    use RefreshDatabase;

    public function testCreateUser()
    {
        $user = User::factory()->create([
            'name' => 'John Doe',
            'email' => 'john@example.com',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
        ]);
    }

    public function testUpdateUser()
    {
        $user = User::factory()->create([
            'name' => 'John Doe',
            'email' => 'john@example.com',
        ]);

        $user->update(['name' => 'Jane Doe']);

        $this->assertDatabaseHas('users', [
            'email' => 'john@example.com',
            'name' => 'Jane Doe',
        ]);
    }

    public function testDeleteUser()
    {
        $user = User::factory()->create([
            'email' => 'john@example.com',
        ]);

        $user->delete();

        $this->assertDatabaseMissing('users', [
            'email' => 'john@example.com',
        ]);
    }
}
```

## Resumo

Os testes de banco de dados são fundamentais para garantir que as operações de CRUD e transações funcionem corretamente e que os dados permaneçam íntegros. Com PHPUnit e Pest, você pode escrever testes de banco de dados de forma eficiente e garantir a qualidade das interações com o banco de dados na sua aplicação Laravel.
