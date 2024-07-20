# Soft Deletes

O Eloquent oferece suporte a soft deletes, permitindo marcar registros como deletados sem removê-los do banco de dados.

## Habilitando Soft Deletes

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class User extends Model
{
    use SoftDeletes;
}
```

## Usando Soft Deletes

```php
$user = User::find(1);
$user->delete();

// Restaurando um registro deletado
$user = User::withTrashed()->find(1);
$user->restore();
```
