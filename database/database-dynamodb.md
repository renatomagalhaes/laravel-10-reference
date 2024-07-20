# DynamoDB

DynamoDB é um banco de dados NoSQL gerenciado oferecido pela AWS, conhecido por sua alta disponibilidade e escalabilidade. Podemos integrar o DynamoDB ao Laravel usando o pacote `baopham/laravel-dynamodb`.

## Instalação

Para instalar o pacote `baopham/laravel-dynamodb`, use o Composer:

```bash
composer require baopham/laravel-dynamodb
```

## Configuração

Para configurar o DynamoDB, adicione a configuração da conexão no arquivo `config/database.php`:

```php
'dynamodb' => [
    'driver' => 'dynamodb',
    'key' => env('AWS_ACCESS_KEY_ID'),
    'secret' => env('AWS_SECRET_ACCESS_KEY'),
    'region' => env('AWS_DEFAULT_REGION', 'us-east-1'),
    'table_prefix' => env('DYNAMODB_TABLE_PREFIX', ''),
],
```

## Definindo Modelos

Para definir modelos que utilizam DynamoDB, use a classe `Baopham\DynamoDb\DynamoDbModel` em vez de `Illuminate\Database\Eloquent\Model`.

### Exemplo de Modelo

```php
namespace App\Models;

use Baopham\DynamoDb\DynamoDbModel;

class User extends DynamoDbModel
{
    protected $table = 'users';
}
```

## Consultas Simples

Você pode usar Eloquent para fazer consultas ao DynamoDB com algumas adaptações.

### Exemplo de Consulta

```php
$users = User::all();
$user = User::find('60b8d2950c7e4b24d8f5ad10');
$activeUsers = User::where('active', true)->get();
```
