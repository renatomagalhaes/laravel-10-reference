# Update

Facades no Laravel oferecem uma maneira prática de atualizar registros no banco de dados. Utilizar facades para operações de atualização torna o código mais expressivo e fácil de entender.

## Utilizando Facades para Atualizar Registros

As facades podem ser usadas para realizar atualizações em registros existentes no banco de dados. Vamos ver como podemos usar a facade DB para atualizar dados.

### Exemplo Básico de Atualização

```php
use Illuminate\Support\Facades\DB;

DB::table('users')
    ->where('id', 1)
    ->update(['name' => 'John Doe']);
```

### Usando Eloquent com Facades

Além de utilizar a facade DB, você pode utilizar facades junto com Eloquent para atualizar registros.

```php
use App\Models\User;

$user = User::find(1);
$user->name = 'Jane Doe';
$user->save();
```

### Atualizações em Massa

Você pode realizar atualizações em massa em vários registros ao mesmo tempo.

```php
DB::table('users')
    ->where('active', 0)
    ->update(['active' => 1]);
```

### Incrementando e Decrementando Valores

Você pode incrementar ou decrementar valores numéricos em um campo específico.

```php
DB::table('products')
    ->where('id', 1)
    ->increment('stock', 10);
```

```php
DB::table('products')
    ->where('id', 1)
    ->decrement('stock', 5);
```

### Atualizações Condicionais

Você pode adicionar condições às suas atualizações para garantir que apenas os registros que atendem a certos critérios sejam modificados.

```php
DB::table('users')
    ->where('email', 'like', '%@example.com')
    ->update(['verified' => 1]);
```

## Resumo

Utilizar facades para atualizar registros no banco de dados é uma maneira eficiente de realizar operações de atualização. As facades simplificam o acesso a várias funcionalidades do framework, facilitando a realização de operações de atualização.
