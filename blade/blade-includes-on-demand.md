# Inclusões Sob Demanda

Blade permite incluir outras views dentro de uma view principal de maneira dinâmica. Isso é útil para reutilizar partes de código e tornar as views mais modulares e fáceis de manter.

## Diretiva `@include`

A diretiva `@include` é usada para incluir uma view dentro de outra. Você pode passar dados para a view incluída utilizando um array associativo.

### Exemplo de Uso da Diretiva `@include`

Crie uma view parcial chamada `_header.blade.php` em `resources/views/partials`:

```php
<!-- resources/views/partials/_header.blade.php -->
<header>
    <h1>{{ $title }}</h1>
</header>
```

Inclua essa view em outra view e passe os dados necessários:

```php
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    @include('partials._header', ['title' => 'Bem-vindo'])

    <p>Conteúdo principal da página.</p>
</body>
</html>
```

## Diretiva `@includeWhen`

A diretiva `@includeWhen` é usada para incluir uma view somente quando uma determinada condição é verdadeira.

### Exemplo de Uso da Diretiva `@includeWhen`

```php
@includeWhen($user->isAdmin(), 'partials._admin_panel', ['user' => $user])
```

## Diretiva `@includeIf`

A diretiva `@includeIf` é usada para incluir uma view se ela existir. Se a view não existir, nenhuma exceção será lançada.

### Exemplo de Uso da Diretiva `@includeIf`

```php
@includeIf('partials._non_existent_view', ['data' => $data])
```

## Diretiva `@includeUnless`

A diretiva `@includeUnless` é usada para incluir uma view somente quando uma determinada condição é falsa.

### Exemplo de Uso da Diretiva `@includeUnless`

```php
@includeUnless($user->isAdmin(), 'partials._user_panel', ['user' => $user])
```

## Diretiva `@each`

A diretiva `@each` é usada para iterar sobre uma coleção e incluir uma view para cada item da coleção. Você pode especificar uma view de fallback para quando a coleção estiver vazia.

### Exemplo de Uso da Diretiva `@each`

Crie uma view parcial chamada `_user.blade.php` em `resources/views/partials`:

```php
<!-- resources/views/partials/_user.blade.php -->
<p>Nome: {{ $user->name }}</p>
```

Inclua essa view para cada item da coleção de usuários:

```php
<!-- resources/views/user_list.blade.php -->
@each('partials._user', $users, 'user', 'partials._no_users')
```

Crie a view de fallback `_no_users.blade.php` em `resources/views/partials`:

```php
<!-- resources/views/partials/_no_users.blade.php -->
<p>Não há usuários.</p>
```

## Resumo

Blade oferece diversas diretivas para incluir views sob demanda, tornando a criação de templates dinâmicos e modulares mais fácil e eficiente. Utilizando `@include`, `@includeWhen`, `@includeIf`, `@includeUnless` e `@each`, você pode gerenciar a inclusão de partes de código de maneira flexível e expressiva.
