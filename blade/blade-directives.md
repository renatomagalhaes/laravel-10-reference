# Diretivas Blade

Blade oferece uma série de diretivas que facilitam a construção de templates de maneira eficiente e expressiva. Essas diretivas são comandos especiais que Blade processa antes de renderizar a view.

## Diretivas Básicas

### `@if`, `@elseif`, `@else`, `@endif`

A diretiva `@if` permite condicionalmente exibir conteúdo com base em uma expressão.

```php
@if ($user->isAdmin())
    <p>Bem-vindo, administrador!</p>
@else
    <p>Bem-vindo, usuário!</p>
@endif
```

### `@unless`

A diretiva `@unless` é o inverso do `@if`.

```php
@unless ($user->isAdmin())
    <p>Você não tem permissões de administrador.</p>
@endunless
```

### `@isset`, `@empty`

As diretivas `@isset` e `@empty` são usadas para verificar se uma variável está definida ou vazia, respectivamente.

```php
@isset($records)
    <p>Existem registros disponíveis.</p>
@endisset

@empty($records)
    <p>Não há registros disponíveis.</p>
@endempty
```

### `@auth`, `@guest`

As diretivas `@auth` e `@guest` são usadas para verificar se o usuário está autenticado ou é um visitante.

```php
@auth
    <p>Bem-vindo, {{ Auth::user()->name }}</p>
@endauth

@guest
    <p>Bem-vindo, visitante!</p>
@endguest
```

### `@switch`, `@case`, `@default`, `@endswitch`

A diretiva `@switch` é usada para construir uma estrutura de switch-case.

```php
@switch($i)
    @case(1)
        <p>Primeiro caso...</p>
        @break

    @case(2)
        <p>Segundo caso...</p>
        @break

    @default
        <p>Valor padrão...</p>
@endswitch
```

## Diretivas de Loop

### `@for`, `@endfor`

A diretiva `@for` é usada para criar um loop `for`.

```php
@for ($i = 0; $i < 10; $i++)
    <p>O valor é {{ $i }}</p>
@endfor
```

### `@foreach`, `@endforeach`

A diretiva `@foreach` é usada para iterar sobre uma coleção.

```php
@foreach ($users as $user)
    <p>Nome: {{ $user->name }}</p>
@endforeach
```

### `@forelse`, `@empty`, `@endforelse`

A diretiva `@forelse` é usada para iterar sobre uma coleção, exibindo um conteúdo alternativo se a coleção estiver vazia.

```php
@forelse ($users as $user)
    <p>Nome: {{ $user->name }}</p>
@empty
    <p>Não há usuários.</p>
@endforelse
```

### `@while`, `@endwhile`

A diretiva `@while` é usada para criar um loop `while`.

```php
@while (true)
    <p>Este loop é infinito.</p>
@endwhile
```

## Diretivas de Comentário

### `{{-- --}}`

A diretiva de comentário é usada para adicionar comentários que não serão exibidos no HTML renderizado.

```php
{{-- Este é um comentário Blade. --}}
```

## Diretivas de Inclusão

### `@include`

A diretiva `@include` é usada para incluir outras views dentro de uma view.

```php
@include('header')
<p>Conteúdo principal da página.</p>
@include('footer')
```

## Diretivas de Extensão de Layout

### `@extends`, `@section`, `@yield`

Essas diretivas são usadas para definir e estender layouts.

```php
{{-- layout.blade.php --}}
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title')</title>
</head>
<body>
    @yield('content')
</body>
</html>

{{-- home.blade.php --}}
@extends('layout')

@section('title', 'Página Inicial')

@section('content')
    <p>Bem-vindo à página inicial!</p>
@endsection
```

## Resumo

As diretivas Blade tornam a criação de templates mais fácil e expressiva, permitindo a inclusão de lógica e estrutura de controle diretamente nas views. Com uma ampla variedade de diretivas disponíveis, Blade é uma ferramenta poderosa para desenvolver interfaces dinâmicas no Laravel.
