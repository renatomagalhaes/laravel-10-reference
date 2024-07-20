# Casting de Atributos

O Eloquent permite o casting de atributos para tipos específicos.

## Exemplo de Casting

```php
class User extends Model
{
    protected $casts = [
        'is_admin' => 'boolean',
        'birthday' => 'date',
    ];
}
```
