# Cenário de Banco Master e Réplica

No cenário de banco master e réplica, as operações de leitura podem ser direcionadas para a réplica, enquanto as operações de escrita são direcionadas para o banco master.

## Configuração no Arquivo `config/database.php`

```php
'mysql' => [
    'read' => [
        'host' => [
            env('DB_HOST_READ', '127.0.0.1'),
        ],
    ],
    'write' => [
        'host' => [
            env('DB_HOST_WRITE', '127.0.0.1'),
        ],
    ],
    'driver' => 'mysql',
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
```

## Direcionando Operações de Leitura e Escrita

No Laravel, operações de leitura e escrita são automaticamente direcionadas com base nas configurações acima. As consultas que utilizam o método `get`, por exemplo, serão direcionadas para os hosts configurados em `read`, enquanto as operações como `insert`, `update`, e `delete` serão direcionadas para os hosts configurados em `write`.

## Exemplo de Consulta de Leitura

```php
$users = DB::table('users')->get();
```

## Exemplo de Operação de Escrita

```php
DB::table('users')->insert([
    'name' => 'John Doe',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);
```
