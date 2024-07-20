# CRUD

O Eloquent facilita as operações de criação, leitura, atualização e deleção.

## Criação

```php
$user = User::create(['name' => 'John Doe', 'email' => 'john@example.com', 'password' => bcrypt('password')]);
```

## Leitura

```php
$users = User::all();
```

## Atualização

```php
$user = User::find(1);
$user->email = 'newemail@example.com';
$user->save();
```

## Deleção

```php
$user = User::find(1);
$user->delete();
```
