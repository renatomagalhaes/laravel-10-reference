# Testes com Factories e Seeders

Factories e seeders são ferramentas poderosas no Laravel que facilitam a criação de dados de teste realistas e consistentes. Usar essas ferramentas em testes ajuda a garantir que a aplicação funcione corretamente com dados semelhantes aos que serão encontrados em produção.

## Importância de Factories e Seeders nos Testes

### Benefícios

- **Realismo:** Permitem a criação de dados de teste que se assemelham aos dados de produção.
- **Reprodutibilidade:** Garantem que os testes sejam executados de maneira consistente com os mesmos dados.
- **Facilidade:** Simplificam a criação de dados complexos e estruturados para testes.

## Configuração de Factories

### Definindo Factories

Factories são definidas na pasta `database/factories`. Elas permitem a criação rápida de modelos com dados fictícios.

#### Exemplo de Factory

Crie uma factory para o modelo `User` em `database/factories/UserFactory.php`:

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

### Usando Factories nos Testes

Para usar factories em seus testes, utilize o método `factory` para criar instâncias do modelo.

#### Exemplo de Teste com Factory

Crie um teste que utiliza a factory `User`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\User;

class UserFactoryTest extends TestCase
{
    use RefreshDatabase;

    public function testUserFactory()
    {
        $user = User::factory()->create([
            'email' => 'test@example.com',
        ]);

        $this->assertDatabaseHas('users', [
            'email' => 'test@example.com',
        ]);
    }
}
```

## Configuração de Seeders

### Definindo Seeders

Seeders são usados para popular o banco de dados com dados iniciais. Eles são definidos na pasta `database/seeders`.

#### Exemplo de Seeder

Crie um seeder para o modelo `User` em `database/seeders/UserSeeder.php`:

```php
namespace Database\Seeders;

use Illuminate\Database\Seeder;
use App\Models\User;

class UserSeeder extends Seeder
{
    public function run()
    {
        User::factory()->count(50)->create();
    }
}
```

### Usando Seeders nos Testes

Para usar seeders em seus testes, chame o método `seed` para popular o banco de dados com os dados definidos.

#### Exemplo de Teste com Seeder

Crie um teste que utiliza o seeder `UserSeeder`:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;

class UserSeederTest extends TestCase
{
    use RefreshDatabase;

    public function testUserSeeder()
    {
        $this->seed(\Database\Seeders\UserSeeder::class);

        $this->assertDatabaseCount('users', 50);
    }
}
```

## Criando Dados de Teste Complexos

### Usando Relacionamentos

Factories podem ser usadas para criar dados complexos com relacionamentos.

#### Exemplo de Factory com Relacionamentos

Crie uma factory para o modelo `Post` que pertence a um `User`:

```php
namespace Database\Factories;

use App\Models\Post;
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;

class PostFactory extends Factory
{
    protected $model = Post::class;

    public function definition()
    {
        return [
            'title' => $this->faker->sentence,
            'body' => $this->faker->paragraph,
            'user_id' => User::factory(),
        ];
    }
}
```

### Exemplo de Teste com Dados Complexos

Crie um teste que verifica a criação de um post com um usuário relacionado:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\Post;

class PostFactoryTest extends TestCase
{
    use RefreshDatabase;

    public function testPostFactory()
    {
        $post = Post::factory()->create();

        $this->assertDatabaseHas('posts', [
            'id' => $post->id,
            'user_id' => $post->user_id,
        ]);

        $this->assertDatabaseHas('users', [
            'id' => $post->user_id,
        ]);
    }
}
```

## Práticas Recomendadas

### Manter Factories e Seeders Atualizados

Certifique-se de que suas factories e seeders estejam sempre atualizadas com as alterações no esquema do banco de dados.

### Usar Dados Realistas

Use dados realistas em suas factories para garantir que os testes sejam representativos do ambiente de produção.

### Testar Seeders

Teste seus seeders para garantir que eles populam o banco de dados corretamente, especialmente após alterações no esquema.

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

### Modelo Post

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    use HasFactory;

    protected $fillable = [
        'title', 'body', 'user_id',
    ];

    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

### Teste de Factories e Seeders

No arquivo `tests/Feature/PostFactoryTest.php`, escreva o teste:

```php
namespace Tests\Feature;

use Illuminate\Foundation\Testing\RefreshDatabase;
use Tests\TestCase;
use App\Models\Post;

class PostFactoryTest extends TestCase
{
    use RefreshDatabase;

    public function testPostFactory()
    {
        $post = Post::factory()->create();

        $this->assertDatabaseHas('posts', [
            'id' => $post->id,
            'user_id' => $post->user_id,
        ]);

        $this->assertDatabaseHas('users', [
            'id' => $post->user_id,
        ]);
    }
}
```

## Resumo

Factories e seeders são ferramentas essenciais para criar dados de teste realistas e consistentes. Eles permitem testar interações complexas e garantir que sua aplicação funcione corretamente com dados semelhantes aos encontrados em produção. Com PHPUnit e Pest, você pode usar essas ferramentas para melhorar a qualidade e a confiabilidade dos seus testes no Laravel.
