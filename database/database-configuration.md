# Configuração do Banco de Dados

Antes de começar a usar o banco de dados, é necessário configurar as credenciais de conexão no arquivo `.env`.

## Exemplo de Configuração no Arquivo .env

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=nome_do_banco
DB_USERNAME=usuario
DB_PASSWORD=senha
```

### Conexão com Múltiplos Bancos de Dados

Laravel permite a configuração de múltiplas conexões de banco de dados. Você pode definir várias conexões no arquivo `config/database.php`.

```php
'mysql' => [
    'driver' => 'mysql',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', '3306'),
    'database' => env('DB_DATABASE', 'forge'),
    'username' => env('DB_USERNAME', 'forge'),
    'password' => env('DB_PASSWORD', ''),
    'unix_socket' => env('DB_SOCKET', ''),
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
    'strict' => true,
    'engine' => null,
],

'mysql2' => [
    'driver' => 'mysql',
    'host' => env('DB_HOST_SECONDARY', '127.0.0.1'),
    'port' => env('DB_PORT_SECONDARY', '3306'),
    'database' => env('DB_DATABASE_SECONDARY', 'forge'),
    'username' => env('DB_USERNAME_SECONDARY', 'forge'),
    'password' => env('DB_PASSWORD_SECONDARY', ''),
    'unix_socket' => env('DB_SOCKET_SECONDARY', ''),
    'charset' => 'utf8mb4',
    'collation' => 'utf8mb4_unicode_ci',
    'prefix' => '',
    'strict' => true,
    'engine' => null,
],
```

### Alterando a Conexão em Tempo de Execução

Você pode alterar a conexão de banco de dados em tempo de execução usando o método `setConnection`.

```php
use App\Models\User;

// Usando a conexão padrão
$users = User::all();

// Mudando para a conexão mysql2
$users = User::on('mysql2')->get();
```
