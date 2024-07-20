# MongoDB

MongoDB é um banco de dados NoSQL amplamente utilizado para armazenar dados JSON-like. No Laravel, podemos usar pacotes como `jenssegers/laravel-mongodb` para integrar o MongoDB.

## Instalação

Para instalar o pacote `jenssegers/laravel-mongodb`, use o Composer:

```bash
composer require jenssegers/mongodb
```

## Configuração

Após instalar o pacote, adicione a configuração da conexão no arquivo `config/database.php`:

```php
'mongodb' => [
    'driver' => 'mongodb',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', 27017),
    'database' => env('DB_DATABASE', 'homestead'),
    'username' => env('DB_USERNAME', 'homestead'),
    'password' => env('DB_PASSWORD', 'secret'),
    'options' => [
        'database' => env('DB_AUTHENTICATION_DATABASE', 'admin'), // se necessário
    ],
],
```

## Definindo Modelos

Para definir modelos que utilizam MongoDB, use a classe `Jenssegers\Mongodb\Eloquent\Model` em vez de `Illuminate\Database\Eloquent\Model`.

### Exemplo de Modelo

```php
namespace App\Models;

use Jenssegers\Mongodb\Eloquent\Model as Eloquent;

class User extends Eloquent
{
    protected $connection = 'mongodb';
}
```

## Consultas Simples

Você pode usar Eloquent normalmente para fazer consultas ao MongoDB.

### Exemplo de Consulta

```php
$users = User::all();
$user = User::find('60b8d2950c7e4b24d8f5ad10');
$activeUsers = User::where('active', true)->get();
```
