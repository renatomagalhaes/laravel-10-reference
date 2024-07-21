# Relacionamento contém 1 de muitos

Os relacionamentos "contém 1 de muitos" (has one of many) permitem que você defina uma relação onde uma instância de um modelo possui uma instância relacionada selecionada de outro modelo com base em uma condição específica.

## Definindo um Relacionamento "contém 1 de muitos"

Para definir esse tipo de relacionamento, você deve usar o método `hasOne` com a cláusula `ofMany`.

### Exemplo de Relacionamento "contém 1 de muitos"

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class User extends Model
{
    public function latestOrder()
    {
        return $this->hasOne(Order::class)->latestOfMany();
    }
}
```

## Definindo a Condição para Seleção

Você pode definir a condição que determina qual instância do modelo relacionado deve ser selecionada.

### Exemplo de Seleção Baseada em Data

```php
class User extends Model
{
    public function latestOrder()
    {
        return $this->hasOne(Order::class)->ofMany('created_at', 'max');
    }
}
```

### Exemplo de Seleção Baseada em Outro Campo

```php
class User extends Model
{
    public function highestPriceOrder()
    {
        return $this->hasOne(Order::class)->ofMany('price', 'max');
    }
}
```

## Consultando um Relacionamento "contém 1 de muitos"

Você pode acessar o relacionamento "contém 1 de muitos" usando métodos dinâmicos Eloquent.

### Exemplo de Consulta

```php
$user = User::find(1);
$latestOrder = $user->latestOrder;
```

## Criando e Salvando Modelos Relacionados

Você pode criar e salvar modelos relacionados usando os métodos `save` e `create`, mas precisa garantir que a lógica de seleção do relacionamento seja respeitada.

### Exemplo de Criação e Salvamento

```php
$user = User::find(1);
$order = new Order(['price' => 100, 'created_at' => now()]);
$user->orders()->save($order);
```

## Atualizando Modelos Relacionados

Você pode atualizar modelos relacionados usando o método `update`.

### Exemplo de Atualização

```php
$user = User::find(1);
$user->latestOrder()->update(['price' => 150]);
```

## Carga Adiantada (Eager Loading)

Para otimizar a performance, você pode usar a carga adiantada (eager loading) para buscar os relacionamentos junto com o modelo principal.

### Exemplo de Carga Adiantada

```php
$users = User::with('latestOrder')->get();
```

## Resumo

Os relacionamentos "contém 1 de muitos" são úteis para representar associações onde você precisa selecionar uma instância específica de um conjunto baseado em uma condição. O Eloquent facilita a definição, consulta, criação, atualização e exclusão desses relacionamentos de maneira eficiente.
