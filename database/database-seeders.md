# Seeders

Seeders são usados para popular o banco de dados com dados de teste ou iniciais.

## Criando um Seeder

Para criar um seeder, use o comando Artisan `make:seeder`.

```bash
php artisan make:seeder UsersTableSeeder
```

## Estrutura de um Seeder

```php
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\DB;
use Illuminate\Support\Str;

class UsersTableSeeder extends Seeder
{
    /**
     * Run the database seeds.
     *
     * @return void
     */
    public function run()
    {
        DB::table('users')->insert([
            'name' => Str::random(10),
            'email' => Str::random(10).'@example.com',
            'password' => bcrypt('password'),
        ]);
    }
}
```

## Executando Seeders

Para executar um seeder específico ou todos os seeders, use os comandos Artisan `db:seed` ou `db:seed --class`.

```bash
php artisan db:seed
php artisan db:seed --class=UsersTableSeeder
```
