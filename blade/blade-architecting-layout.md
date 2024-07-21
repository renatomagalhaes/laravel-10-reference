# Arquitetando um Layout

O Blade facilita a criação de layouts reutilizáveis, permitindo que você defina uma estrutura básica para suas páginas e depois estenda essa estrutura em views individuais. Isso ajuda a manter o código organizado e consistente.

## Criando um Layout Base

Um layout base define a estrutura comum que será compartilhada por várias views. Use a diretiva `@yield` para definir seções que serão preenchidas nas views que estendem este layout.

### Exemplo de Layout Base

```php
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Minha Aplicação Laravel')</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    <header>
        @include('partials.header')
    </header>

    <main>
        @yield('content')
    </main>

    <footer>
        @include('partials.footer')
    </footer>

    <script src="{{ asset('js/app.js') }}"></script>
</body>
</html>
```

### Exemplo de Partiais

Crie partials para o cabeçalho e rodapé:

```php
<!-- resources/views/partials/header.blade.php -->
<header>
    <h1>Minha Aplicação</h1>
    <nav>
        <ul>
            <li><a href="/">Home</a></li>
            <li><a href="/about">Sobre</a></li>
            <li><a href="/contact">Contato</a></li>
        </ul>
    </nav>
</header>
```

```php
<!-- resources/views/partials/footer.blade.php -->
<footer>
    <p>&copy; 2024 Minha Aplicação. Todos os direitos reservados.</p>
</footer>
```

## Estendendo o Layout

Use a diretiva `@extends` para estender o layout base e a diretiva `@section` para preencher as seções definidas no layout.

### Exemplo de View que Estende o Layout

```php
<!-- resources/views/home.blade.php -->
@extends('layouts.app')

@section('title', 'Página Inicial')

@section('content')
    <h2>Bem-vindo à Página Inicial</h2>
    <p>Este é o conteúdo da página inicial.</p>
@endsection
```

## Seções Opcionais

Você pode definir seções opcionais que só serão exibidas se forem preenchidas. Use a função `@hasSection` para verificar se uma seção foi preenchida.

### Exemplo de Seção Opcional

```php
<!-- resources/views/layouts/app.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>@yield('title', 'Minha Aplicação Laravel')</title>
    <link rel="stylesheet" href="{{ asset('css/app.css') }}">
</head>
<body>
    <header>
        @include('partials.header')
    </header>

    <main>
        @yield('content')
    </main>

    @hasSection('sidebar')
        <aside>
            @yield('sidebar')
        </aside>
    @endif

    <footer>
        @include('partials.footer')
    </footer>

    <script src="{{ asset('js/app.js') }}"></script>
</body>
</html>
```

```php
<!-- resources/views/home.blade.php -->
@extends('layouts.app')

@section('title', 'Página Inicial')

@section('content')
    <h2>Bem-vindo à Página Inicial</h2>
    <p>Este é o conteúdo da página inicial.</p>
@endsection

@section('sidebar')
    <p>Este é o conteúdo da barra lateral.</p>
@endsection
```

## Resumo

Arquitetar um layout com Blade permite criar uma estrutura reutilizável e consistente para suas views. Utilizando diretivas como `@extends`, `@yield`, `@section` e `@hasSection`, você pode definir e estender layouts de maneira eficiente, mantendo seu código organizado e fácil de manter.
