# Dividir / Modularizar

Modularizar suas views no Blade permite dividir grandes arquivos de template em partes menores e mais gerenciáveis. Isso facilita a manutenção, a reutilização de código e a organização das suas views.

## Diretiva `@include`

A diretiva `@include` é usada para incluir outras views dentro de uma view principal. Isso é útil para dividir partes comuns da interface, como cabeçalhos, rodapés e barras laterais.

### Exemplo de Uso da Diretiva `@include`

```php
<!-- resources/views/layouts/main.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    @include('partials.header')

    <div class="content">
        @yield('content')
    </div>

    @include('partials.footer')
</body>
</html>
```

```php
<!-- resources/views/partials/header.blade.php -->
<header>
    <h1>Header da Aplicação</h1>
</header>
```

```php
<!-- resources/views/partials/footer.blade.php -->
<footer>
    <p>Rodapé da Aplicação</p>
</footer>
```

```php
<!-- resources/views/pages/home.blade.php -->
@extends('layouts.main')

@section('content')
    <h2>Bem-vindo à Página Inicial</h2>
    <p>Conteúdo da página inicial.</p>
@endsection
```

## Diretiva `@section` e `@yield`

A diretiva `@section` é usada para definir uma seção de conteúdo que pode ser substituída em views que estendem um layout. A diretiva `@yield` é usada para renderizar o conteúdo dessas seções.

### Exemplo de Uso das Diretivas `@section` e `@yield`

```php
<!-- resources/views/layouts/main.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Minha Aplicação Laravel')</title>
</head>
<body>
    @include('partials.header')

    <div class="content">
        @yield('content')
    </div>

    @include('partials.footer')
</body>
</html>
```

```php
<!-- resources/views/pages/home.blade.php -->
@extends('layouts.main')

@section('title', 'Página Inicial')

@section('content')
    <h2>Bem-vindo à Página Inicial</h2>
    <p>Conteúdo da página inicial.</p>
@endsection
```

## Diretiva `@component`

A diretiva `@component` é usada para incluir componentes de view que podem receber dados e renderizar conteúdo reutilizável.

### Exemplo de Uso da Diretiva `@component`

Crie um componente chamado `alert`:

```php
<!-- resources/views/components/alert.blade.php -->
<div class="alert alert-{{ $type }}">
    {{ $slot }}
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/pages/home.blade.php -->
@extends('layouts.main')

@section('content')
    <h2>Bem-vindo à Página Inicial</h2>
    @component('components.alert', ['type' => 'danger'])
        <strong>Atenção!</strong> Isso é uma mensagem de alerta.
    @endcomponent
@endsection
```

## Diretiva `@each`

A diretiva `@each` é usada para iterar sobre uma coleção e incluir uma view para cada item da coleção.

### Exemplo de Uso da Diretiva `@each`

Crie uma view parcial chamada `_user.blade.php`:

```php
<!-- resources/views/partials/_user.blade.php -->
<p>Nome: {{ $user->name }}</p>
```

Inclua essa view para cada item da coleção de usuários:

```php
<!-- resources/views/pages/user_list.blade.php -->
@extends('layouts.main')

@section('content')
    @each('partials._user', $users, 'user', 'partials._no_users')
@endsection
```

Crie a view de fallback `_no_users.blade.php`:

```php
<!-- resources/views/partials/_no_users.blade.php -->
<p>Não há usuários.</p>
```

## Resumo

Modularizar suas views no Blade permite dividir grandes arquivos de template em partes menores e mais gerenciáveis. Utilizando diretivas como `@include`, `@section`, `@yield`, `@component` e `@each`, você pode criar templates dinâmicos e organizados que são fáceis de manter e reutilizar.
