# Mass Assignment

Protegendo contra atribuição massiva no Eloquent.

## Protegendo Atribuição Massiva

```php
class User extends Model
{
    protected $fillable = ['name', 'email', 'password'];
}
```
