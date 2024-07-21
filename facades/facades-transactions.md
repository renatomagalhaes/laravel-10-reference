# Transações - Commit e Rollback

Facades no Laravel fornecem uma maneira prática de gerenciar transações no banco de dados, permitindo que você execute múltiplas operações como uma única unidade de trabalho. Isso é essencial para garantir a integridade dos dados em casos de falhas ou erros durante a execução de operações.

## Utilizando Facades para Gerenciar Transações

As facades podem ser usadas para gerenciar transações de banco de dados de forma simples e eficiente. Vamos ver como podemos usar a facade DB para iniciar, commitar e reverter transações.

### Exemplo Básico de Transação

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    DB::table('users')->update(['active' => 0]);
    DB::table('posts')->delete();
});
```

### Transações Manuais

Além de utilizar o método `transaction`, você pode gerenciar transações manualmente usando `beginTransaction`, `commit` e `rollBack`.

### Exemplo de Transações Manuais

```php
use Illuminate\Support\Facades\DB;

try {
    DB::beginTransaction();

    DB::table('users')->update(['active' => 0]);
    DB::table('posts')->delete();

    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}
```

### Aninhamento de Transações

O Laravel suporta o aninhamento de transações, permitindo que você inicie uma transação dentro de outra.

### Exemplo de Aninhamento de Transações

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    DB::table('users')->update(['active' => 0]);

    DB::transaction(function () {
        DB::table('posts')->delete();
    });
});
```

### Revertendo Transações Parciais

Se você precisar reverter apenas uma parte das operações, pode usar o método `DB::rollback` dentro do bloco de transação.

### Exemplo de Reversão Parcial

```php
use Illuminate\Support\Facades\DB;

DB::transaction(function () {
    DB::table('users')->update(['active' => 0]);

    try {
        DB::transaction(function () {
            DB::table('posts')->delete();
        });
    } catch (\Exception $e) {
        // Reverte apenas a transação interna
        DB::rollBack();
    }
});
```

## Resumo

Utilizar facades para gerenciar transações no banco de dados é uma maneira eficiente de garantir a integridade dos dados durante operações complexas. As facades simplificam o acesso a várias funcionalidades do framework, facilitando a implementação de transações seguras.
