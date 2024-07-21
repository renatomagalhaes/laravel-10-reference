# Relacionamento de muitos pra muitos

Os relacionamentos de muitos para muitos são usados para definir uma relação onde múltiplas instâncias de um modelo estão associadas a múltiplas instâncias de outro modelo.

## Definindo um Relacionamento de muitos pra muitos

Para definir um relacionamento de muitos para muitos, você deve usar o método `belongsToMany` em ambos os modelos e definir a tabela intermediária que armazena as chaves estrangeiras.

### Exemplo de Relacionamento de muitos pra muitos

Vamos considerar um exemplo onde `User` e `Role` têm uma relação de muitos para muitos através da tabela `role_user`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
}

class Role extends Model
{
    public function users()
    {
        return $this->belongsToMany(User::class);
    }
}
```

## Estrutura da Tabela Intermediária

A tabela intermediária deve conter as chaves estrangeiras que referenciam as tabelas principais.

### Exemplo de Estrutura da Tabela Intermediária

```php
Schema::create('role_user', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->timestamps();
});
```

## Consultando um Relacionamento de muitos pra muitos

Você pode acessar o relacionamento de muitos para muitos usando métodos dinâmicos Eloquent.

### Exemplo de Consulta

```php
$user = User::find(1);
$roles = $user->roles;
```

### Exemplo de Consulta Reversa

```php
$role = Role::find(1);
$users = $role->users;
```

## Sincronizando Relacionamentos

Você pode sincronizar relacionamentos usando o método `sync`, que ajusta as relações intermediárias para corresponder ao array fornecido de IDs.

### Exemplo de Sincronização

```php
$user = User::find(1);
$user->roles()->sync([1, 2, 3]);
```

## Anexando e Desanexando Relacionamentos

Você pode adicionar e remover relações intermediárias usando os métodos `attach` e `detach`.

### Exemplo de Anexar

```php
$user = User::find(1);
$user->roles()->attach(1);
```

### Exemplo de Desanexar

```php
$user = User::find(1);
$user->roles()->detach(1);
```

## Relacionamentos com Dados Adicionais

A tabela intermediária pode conter colunas adicionais além das chaves estrangeiras. Você pode acessar esses dados adicionais ao trabalhar com os relacionamentos.

### Exemplo de Relacionamento com Dados Adicionais

```php
Schema::create('role_user', function (Blueprint $table) {
    $table->id();
    $table->foreignId('user_id')->constrained()->onDelete('cascade');
    $table->foreignId('role_id')->constrained()->onDelete('cascade');
    $table->boolean('active');
    $table->timestamps();
});

class User extends Model
{
    public function roles()
    {
        return $this->belongsToMany(Role::class)->withPivot('active');
    }
}

$user = User::find(1);
foreach ($user->roles as $role) {
    echo $role->pivot->active;
}
```

## Resumo

Os relacionamentos de muitos para muitos são essenciais para representar associações onde várias instâncias de um modelo estão relacionadas a várias instâncias de outro modelo. O Eloquent facilita a definição, consulta, criação, atualização e exclusão desses relacionamentos, mantendo o código limpo e organizado.
