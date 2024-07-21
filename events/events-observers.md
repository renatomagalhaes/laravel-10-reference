# Observers

Observers no Laravel fornecem uma maneira conveniente de agrupar a lógica relacionada aos eventos de um modelo. Eles permitem que você defina métodos que serão chamados automaticamente quando ações específicas ocorrerem em um modelo. Este guia aborda como criar e usar observers no Laravel.

## O que é um Observer?

Um observer é uma classe que ouve eventos em um modelo específico e agrupa a lógica relacionada a esses eventos. Ele permite que você centralize a lógica de eventos, como criação, atualização e exclusão, em uma única classe.

## Criando um Observer

### Passo 1: Criar um Observer

Para criar um observer, use o comando Artisan:

```bash
php artisan make:observer UserObserver --model=User
```

Isso criará um arquivo de observer em `app/Observers/UserObserver.php` com a estrutura básica:

```php
namespace App\Observers;

use App\Models\User;

class UserObserver
{
    public function created(User $user)
    {
        // Lógica para o evento "created"
    }

    public function updated(User $user)
    {
        // Lógica para o evento "updated"
    }

    public function deleted(User $user)
    {
        // Lógica para o evento "deleted"
    }

    // Outros métodos para eventos adicionais
}
```

### Passo 2: Registrar o Observer

Registre o observer no método `boot` do `AppServiceProvider` ou em um provider dedicado:

```php
use App\Models\User;
use App\Observers\UserObserver;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        User::observe(UserObserver);
    }
}
```

## Métodos Disponíveis em um Observer

Os observers podem ouvir uma variedade de eventos do modelo. Aqui estão alguns dos métodos disponíveis:

- `created`: Chamado após a criação de um modelo.
- `updated`: Chamado após a atualização de um modelo.
- `deleted`: Chamado após a exclusão de um modelo.
- `restored`: Chamado após a restauração de um modelo excluído com soft deletes.
- `saving`: Chamado antes de um modelo ser salvo (tanto na criação quanto na atualização).
- `saved`: Chamado após um modelo ser salvo (tanto na criação quanto na atualização).
- `deleting`: Chamado antes de um modelo ser excluído.
- `restoring`: Chamado antes de um modelo ser restaurado.

## Exemplo Completo de Observer

### Passo 1: Criar um Observer

```bash
php artisan make:observer PostObserver --model=Post
```

### Passo 2: Implementar o Observer

Defina o observer em `app/Observers/PostObserver.php`:

```php
namespace App\Observers;

use App\Models\Post;

class PostObserver
{
    public function created(Post $post)
    {
        \Log::info('Post criado: ' . $post->id);
    }

    public function updated(Post $post)
    {
        \Log::info('Post atualizado: ' . $post->id);
    }

    public function deleted(Post $post)
    {
        \Log::info('Post excluído: ' . $post->id);
    }
}
```

### Passo 3: Registrar o Observer

Registre o observer no método `boot` do `AppServiceProvider`:

```php
use App\Models\Post;
use App\Observers\PostObserver;

class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Post::observe(PostObserver);
    }
}
```

## Vantagens dos Observers

### Centralização da Lógica

Observers permitem centralizar a lógica relacionada aos eventos de um modelo em uma única classe, tornando o código mais organizado e fácil de manter.

### Reutilização de Código

Ao usar observers, você pode reutilizar a lógica de eventos em diferentes partes da aplicação, evitando duplicação de código.

### Separação de Preocupações

Observers promovem a separação de preocupações, permitindo que a lógica de eventos seja separada da lógica principal do modelo e do controlador.

## Resumo

Observers no Laravel são uma ferramenta poderosa para agrupar a lógica relacionada aos eventos de um modelo. Eles permitem centralizar a lógica de eventos em uma única classe, facilitando a organização e a manutenção do código. Com a configuração adequada e os exemplos fornecidos, você pode começar a implementar observers na sua aplicação Laravel para melhorar a modularidade e a manutenção do código.
