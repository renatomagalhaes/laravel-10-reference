# Armazenamento de Dados no Cache

Armazenar dados no cache é uma das operações fundamentais para melhorar a performance de uma aplicação Laravel. Este guia aborda como armazenar dados de maneira eficiente e segura, utilizando os diferentes métodos fornecidos pelo Laravel.

## Armazenando Dados no Cache

### Passo 1: Usando o Método put()

O método `put` armazena um item no cache por um tempo especificado (em minutos):

```php
Cache::put('key', 'value', $minutes);
```

### Passo 2: Usando o Método add()

O método `add` só adicionará o item ao cache se ele não existir:

```php
Cache::add('key', 'value', $minutes);
```

### Passo 3: Usando o Método forever()

O método `forever` armazena um item no cache indefinidamente:

```php
Cache::forever('key', 'value');
```

## Armazenando Dados no Cache com Condições

### Usando o Método remember()

O método `remember` recupera um item do cache ou armazena o resultado de um fechamento (closure) se o item não existir:

```php
$value = Cache::remember('key', $minutes, function () {
    return DB::table('users')->get();
});
```

### Usando o Método rememberForever()

O método `rememberForever` é semelhante ao `remember`, mas armazena o item indefinidamente:

```php
$value = Cache::rememberForever('key', function () {
    return DB::table('users')->get();
});
```

## Cache Tags

### Usando Tags para Agrupar Dados

Tags permitem agrupar múltiplos itens de cache e manipular todos eles ao mesmo tempo:

```php
Cache::tags(['people', 'artists'])->put('John', $john, $minutes);
Cache::tags(['people', 'authors'])->put('Anne', $anne, $minutes);
```

### Invalidação por Tags

Você pode invalidar todos os itens de uma ou mais tags:

```php
Cache::tags(['people', 'authors'])->flush();
```

## Cache de Bloco (Partial Cache)

### Cache de Fragmentos de Views

Você pode armazenar fragmentos de views no cache para melhorar a performance de renderização:

```php
@cache('cache-key')
    <div>
        <!-- Conteúdo que será cacheado -->
    </div>
@endcache
```

## Cache de Consultas Eloquent

### Otimização de Consultas com Cache

Você pode armazenar o resultado de consultas Eloquent no cache para evitar consultas repetidas ao banco de dados:

```php
$users = Cache::remember('users', $minutes, function () {
    return User::all();
});
```

## Exemplos Práticos

### Armazenando e Recuperando Dados

```php
// Armazenar um valor
Cache::put('user', $user, 60);

// Recuperar um valor
$user = Cache::get('user');

// Armazenar um valor indefinidamente
Cache::forever('settings', $settings);
```

### Utilizando o Cache para Melhorar Performance

```php
$popularPosts = Cache::remember('popular-posts', 1440, function () {
    return Post::where('views', '>', 1000)->get();
});
```

## Resumo

Armazenar dados no cache é uma prática essencial para melhorar a performance e a escalabilidade de uma aplicação Laravel. Utilizando os métodos fornecidos pelo Laravel, como `put`, `add`, `forever`, `remember`, e `tags`, você pode gerenciar eficientemente o cache e proporcionar uma melhor experiência aos usuários da sua aplicação.
