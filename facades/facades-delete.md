# Delete

Facades no Laravel fornecem uma maneira prática e expressiva de excluir registros do banco de dados. Utilizar facades para operações de deleção torna o código mais claro e fácil de entender.

## Utilizando Facades para Excluir Registros

As facades podem ser usadas para realizar exclusões de registros existentes no banco de dados. Vamos ver como podemos usar a facade DB para deletar dados.

### Exemplo Básico de Exclusão

```php
use Illuminate\Support\Facades\DB;

DB::table('users')->where('id', 1)->delete();
```

### Usando Eloquent com Facades

Além de utilizar a facade DB, você pode utilizar facades junto com Eloquent para excluir registros.

```php
use App\Models\User;

$user = User::find(1);
$user->delete();
```

### Exclusão em Massa

Você pode realizar exclusões em massa em vários registros ao mesmo tempo.

```php
DB::table('users')->where('active', 0)->delete();
```

## Soft Deletes

O Laravel também oferece suporte a soft deletes, que permitem marcar registros como deletados sem removê-los fisicamente do banco de dados.

### Habilitando Soft Deletes

Para habilitar soft deletes em um modelo Eloquent, use o trait `SoftDeletes`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model
{
    use SoftDeletes;
}
```

### Exemplo de Soft Delete

```php
$user = User::find(1);
$user->delete();
```

### Restaurando Registros Deletados

Você pode restaurar registros deletados usando o método `restore`.

```php
$user = User::withTrashed()->find(1);
$user->restore();
```

### Excluindo Permanentemente

Para excluir permanentemente um registro que foi marcado como deletado, use o método `forceDelete`.

```php
$user = User::withTrashed()->find(1);
$user->forceDelete();
```

## Resumo

Utilizar facades para excluir registros no banco de dados é uma maneira eficiente de realizar operações de deleção. As facades simplificam o acesso a várias funcionalidades do framework, e o suporte a soft deletes adiciona flexibilidade na gestão de dados deletados.
