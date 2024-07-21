# Relational Data Integrity

Garantir a integridade dos dados relacionais é crucial para manter a consistência e a confiabilidade das informações em sua aplicação.

## Técnicas de Garantia de Integridade

### Chaves Estrangeiras

Use chaves estrangeiras para manter a integridade referencial entre tabelas.

```php
Schema::create('orders', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->timestamps();
});
```

### Restrições de Unicidade

Use restrições de unicidade para evitar duplicação de dados.

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('email')->unique();
});
```

### Validações no Eloquent

Use validações no Eloquent para garantir a integridade dos dados ao salvar modelos.

```php
class User extends Model
{
    public static function boot()
    {
        parent::boot();

        static::saving(function ($user) {
            if (!filter_var($user->email, FILTER_VALIDATE_EMAIL)) {
                throw new \Exception('Invalid email address');
            }
        });
    }
}
```

## Resumo

Garantir a integridade dos dados relacionais é essencial para manter a consistência e a confiabilidade das informações. Utilizar chaves estrangeiras, restrições de unicidade e validações no Eloquent são práticas recomendadas para assegurar a integridade dos dados.
