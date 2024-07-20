# Query Builder

Query Builder fornece uma interface fluente para criar e executar consultas de banco de dados.

## Exemplo de Uso do Query Builder

```php
$users = DB::table('users')->where('active', 1)->get();
```

## Inserindo Dados

```php
DB::table('users')->insert([
    'name' => 'John',
    'email' => 'john@example.com',
    'password' => bcrypt('password'),
]);
```

## Atualizando Dados

```php
DB::table('users')->where('id', 1)->update(['name' => 'John Doe']);
```

## Deletando Dados

```php
DB::table('users')->where('id', 1)->delete();
```
