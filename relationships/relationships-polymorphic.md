# Relacionamento Polimórfico

Os relacionamentos polimórficos permitem que um modelo pertença a mais de um outro modelo em uma única associação. Isso é útil quando diferentes tipos de modelos podem ter uma relação com um único modelo.

## Relacionamento Polimórfico Um-para-um

Em um relacionamento polimórfico um-para-um, um modelo pode pertencer a mais de um modelo.

### Definindo um Relacionamento Polimórfico Um-para-um

Vamos considerar um exemplo onde um `Photo` pode pertencer tanto a um `User` quanto a um `Product`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Photo extends Model
{
    public function imageable()
    {
        return $this->morphTo();
    }
}

class User extends Model
{
    public function photo()
    {
        return $this->morphOne(Photo::class, 'imageable');
    }
}

class Product extends Model
{
    public function photo()
    {
        return $this->morphOne(Photo::class, 'imageable');
    }
}
```

### Estrutura da Tabela

A tabela `photos` deve conter as colunas `imageable_id` e `imageable_type` para armazenar a chave do modelo pai e o tipo de modelo pai, respectivamente.

```php
Schema::create('photos', function (Blueprint $table) {
    $table->id();
    $table->string('url');
    $table->unsignedBigInteger('imageable_id');
    $table->string('imageable_type');
    $table->timestamps();
});
```

### Consultando um Relacionamento Polimórfico Um-para-um

```php
$user = User::find(1);
$photo = $user->photo;

$product = Product::find(1);
$photo = $product->photo;
```

## Relacionamento Polimórfico Um-para-muitos

Em um relacionamento polimórfico um-para-muitos, um modelo pode ter múltiplos relacionamentos com diferentes tipos de modelos.

### Definindo um Relacionamento Polimórfico Um-para-muitos

Vamos considerar um exemplo onde `Comment` pode pertencer tanto a um `Post` quanto a um `Video`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model
{
    public function commentable()
    {
        return $this->morphTo();
    }
}

class Post extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}

class Video extends Model
{
    public function comments()
    {
        return $this->morphMany(Comment::class, 'commentable');
    }
}
```

### Estrutura da Tabela

A tabela `comments` deve conter as colunas `commentable_id` e `commentable_type` para armazenar a chave do modelo pai e o tipo de modelo pai, respectivamente.

```php
Schema::create('comments', function (Blueprint $table) {
    $table->id();
    $table->text('body');
    $table->unsignedBigInteger('commentable_id');
    $table->string('commentable_type');
    $table->timestamps();
});
```

### Consultando um Relacionamento Polimórfico Um-para-muitos

```php
$post = Post::find(1);
$comments = $post->comments;

$video = Video::find(1);
$comments = $video->comments;
```

## Relacionamento Polimórfico Muitos-para-muitos

Em um relacionamento polimórfico muitos-para-muitos, um modelo pode pertencer a múltiplos modelos e vice-versa.

### Definindo um Relacionamento Polimórfico Muitos-para-muitos

Vamos considerar um exemplo onde `Tag` pode ser aplicada tanto a `Post` quanto a `Video`.

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Tag extends Model
{
    public function posts()
    {
        return $this->morphedByMany(Post::class, 'taggable');
    }

    public function videos()
    {
        return $this->morphedByMany(Video::class, 'taggable');
    }
}

class Post extends Model
{
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}

class Video extends Model
{
    public function tags()
    {
        return $this->morphToMany(Tag::class, 'taggable');
    }
}
```

### Estrutura da Tabela Intermediária

A tabela intermediária `taggables` deve conter as colunas `taggable_id`, `taggable_type` e `tag_id`.

```php
Schema::create('taggables', function (Blueprint $table) {
    $table->id();
    $table->unsignedBigInteger('tag_id');
    $table->unsignedBigInteger('taggable_id');
    $table->string('taggable_type');
    $table->timestamps();
});
```

### Consultando um Relacionamento Polimórfico Muitos-para-muitos

```php
$post = Post::find(1);
$tags = $post->tags;

$video = Video::find(1);
$tags = $video->tags;

$tag = Tag::find(1);
$posts = $tag->posts;
$videos = $tag->videos;
```

## Resumo

Os relacionamentos polimórficos são extremamente úteis para associar um modelo a múltiplos modelos de diferentes tipos. O Eloquent facilita a definição, consulta, criação, atualização e exclusão desses relacionamentos, mantendo o código limpo e eficiente.
