# Relacionamento de 1 pra 1

Os relacionamentos de um para um são usados para definir uma relação onde cada instância de um modelo está associada exatamente a uma instância de outro modelo.

## Definindo um Relacionamento de 1 pra 1

Para definir um relacionamento de um para um, você deve usar o método `hasOne` no modelo pai e o método `belongsTo` no modelo filho.

### Exemplo de Relacionamento de 1 pra 1

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function phone()
    {
        return $this->hasOne(Phone::class);
    }
}

class Phone extends Model
{
    public function user()
    {
        return $this->belongsTo(User::class);
    }
}
```

## Consultando um Relacionamento de 1 pra 1

Você pode acessar o relacionamento de um para um usando métodos dinâmicos Eloquent.

### Exemplo de Consulta

```php
$user = User::find(1);
$phone = $user->phone;
```

### Exemplo de Consulta Reversa

```php
$phone = Phone::find(1);
$user = $phone->user;
```

## Criando e Salvando Modelos Relacionados

Você pode criar e salvar modelos relacionados usando os métodos `save` e `create`.

### Exemplo de Criação e Salvamento

```php
$user = User::find(1);
$phone = new Phone(['number' => '123-456-7890']);
$user->phone()->save($phone);
```

### Exemplo de Uso do Método `create`

```php
$user = User::find(1);
$phone = $user->phone()->create(['number' => '123-456-7890']);
```

## Atualizando Modelos Relacionados

Você pode atualizar modelos relacionados usando o método `update`.

### Exemplo de Atualização

```php
$user = User::find(1);
$user->phone()->update(['number' => '098-765-4321']);
```

## Excluindo Modelos Relacionados

Você pode excluir modelos relacionados usando o método `delete`.

### Exemplo de Exclusão

```php
$user = User::find(1);
$user->phone()->delete();
```

## Resumo

Os relacionamentos de um para um são úteis para ligar modelos que compartilham uma relação única e exclusiva. O Eloquent facilita a definição, consulta, criação, atualização e exclusão desses relacionamentos, mantendo o código limpo e organizado.
