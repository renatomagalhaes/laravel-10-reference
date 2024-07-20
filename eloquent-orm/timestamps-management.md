# Gestão de Timestamps

O Eloquent gerencia automaticamente os campos `created_at` e `updated_at` para você.

## Desabilitando Timestamps

Se você não quiser que o Eloquent gerencie os timestamps automaticamente, desabilite essa funcionalidade.

```php
class User extends Model
{
    public $timestamps = false;
}
```

## Personalizando Timestamps

Você pode personalizar os nomes dos campos de timestamps.

```php
class User extends Model
{
    const CREATED_AT = 'creation_date';
    const UPDATED_AT = 'last_update';
}
```
