# Banco de Leitura e Escrita com CQRS

Command Query Responsibility Segregation (CQRS) é um padrão de design que separa a lógica de leitura e escrita em diferentes modelos, otimizando consultas e comandos de forma independente.

## Implementando CQRS

Para implementar CQRS, você pode ter diferentes bancos de dados ou tabelas para leitura e escrita. Relacionamentos precisam ser gerenciados de maneira que respeitem essa separação.

### Definição de Modelos CQRS

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    // Modelo de escrita
    protected $connection = 'mysql_write';

    public function comments()
    {
        return $this->hasMany(Comment::class);
    }
}

class PostRead extends Model
{
    // Modelo de leitura
    protected $connection = 'mysql_read';

    public function comments()
    {
        return $this->hasMany(CommentRead::class);
    }
}

class Comment extends Model
{
    protected $connection = 'mysql_write';
    public function post()
    {
        return $this->belongsTo(Post::class);
    }
}

class CommentRead extends Model
{
    protected $connection = 'mysql_read';
    public function post()
    {
        return $this->belongsTo(PostRead::class);
    }
}
```

### Sincronização de Bancos de Dados

Para manter os bancos de dados de leitura e escrita sincronizados, você pode utilizar eventos ou filas para replicar dados entre eles.

```php
namespace App\Listeners;

use App\Events\PostCreated;
use App\Models\PostRead;

class ReplicatePostToReadDatabase
{
    public function handle(PostCreated $event)
    {
        $post = $event->post;
        PostRead::create($post->toArray());
    }
}
```

### Consultas CQRS

```php
$postRead = PostRead::with('comments')->find(1);
$comments = $postRead->comments;
```

## Resumo

O padrão CQRS ajuda a otimizar operações de leitura e escrita separando a lógica em diferentes modelos ou bancos de dados. Utilizar facades para gerenciar CQRS facilita a implementação de operações seguras e eficientes, melhorando a performance e escalabilidade da aplicação.
