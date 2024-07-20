# Leitura de Dados

O Eloquent facilita a leitura de dados do banco de dados.

## Métodos de Leitura

```php
// Retornar todos os usuários
$users = User::all();

// Retornar um usuário por ID
$user = User::find(1);

// Retornar o primeiro usuário
$user = User::first();
```
