# Relacionamento de 1 pra muitos

Os relacionamentos de um para muitos são usados para definir uma relação onde uma única instância de um modelo está associada a múltiplas instâncias de outro modelo.

## Definindo um Relacionamento de 1 pra muitos

Para definir um relacionamento de um para muitos, você deve usar o método `hasMany` no modelo pai e o método `belongsTo` no modelo filho.

### Exemplo de Relacionamento de 1 pra muitos

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

class Comment extends Model
{
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
}
```

## Consultando um Relacionamento de 1 pra muitos

Você pode acessar o relacionamento de um para muitos usando métodos dinâmicos Eloquent.

### Exemplo de Consulta

```php
$post = Post::find(1);
$comments = $post->comments;
```

### Exemplo de Consulta Reversa

```php
$comment = Comment::find(1);
$post = $comment->post;
```

## Criando e Salvando Modelos Relacionados

Você pode criar e salvar modelos relacionados usando os métodos `save` e `create`.

### Exemplo de Criação e Salvamento

```php
$post = Post::find(1);
$comment = new Comment(['content' => 'This is a comment']);
$post->comments()->save($comment);
```

### Exemplo de Uso do Método `create`

```php
$post = Post::find(1);
$comment = $post->comments()->create(['content' => 'This is a comment']);
```

## Atualizando Modelos Relacionados

Você pode atualizar modelos relacionados usando o método `update`.

### Exemplo de Atualização

```php
$post = Post::find(1);
$post->comments()->update(['content' => 'Updated comment']);
```

## Excluindo Modelos Relacionados

Você pode excluir modelos relacionados usando o método `delete`.

### Exemplo de Exclusão

```php
$post = Post::find(1);
$post->comments()->delete();
```

## Carga Adiantada (Eager Loading)

Para otimizar a performance, você pode usar a carga adiantada (eager loading) para buscar os relacionamentos junto com o modelo principal.

### Exemplo de Carga Adiantada

```php
$posts = Post::with('comments')->get();
```

## Resumo

Os relacionamentos de um para muitos são essenciais para representar associações comuns entre modelos, como posts e comentários, ou categorias e produtos. O Eloquent torna a definição, consulta, criação, atualização e exclusão desses relacionamentos simples e eficiente.
