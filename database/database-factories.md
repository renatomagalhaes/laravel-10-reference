# Factories

Factories são usadas para gerar modelos de dados de teste de forma conveniente.

## Criando uma Factory

Para criar uma factory, use o comando Artisan `make:factory`.

```bash
php artisan make:factory UserFactory
```

## Estrutura de uma Factory

```php
use App\Models\User;
use Illuminate\Database\Eloquent\Factories\Factory;
use Illuminate\Support\Str;

class UserFactory extends Factory
{
    /**
     * The name of the factory's corresponding model.
     *
     * @var string
     */
    protected $model = User::class;

    /**
     * Define the model's default state.
     *
     * @return array
     */
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

## Usando Factories

Para usar uma factory para criar registros de teste, use o método `factory` em seu modelo.

```php
User::factory()->count(50)->create();
```
