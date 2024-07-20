# Migrations

Migrations são uma maneira conveniente de criar e modificar tabelas no banco de dados. Elas permitem versionar as mudanças no esquema do banco de dados.

## Criando uma Migration

Para criar uma migration, use o comando Artisan `make:migration`.

```bash
php artisan make:migration create_users_table
```

## Estrutura de uma Migration

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateUsersTable extends Migration
{
    /**
     * Run the migrations.
     *
     * @return void
     */
    public function up()
    {
        Schema::create('users', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('email')->unique();
            $table->timestamps();
        });
    }

    /**
     * Reverse the migrations.
     *
     * @return void
     */
    public function down()
    {
        Schema::dropIfExists('users');
    }
}
```

## Executando Migrations

Para executar todas as migrations pendentes, use o comando Artisan `migrate`.

```bash
php artisan migrate
```
