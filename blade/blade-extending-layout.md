# Fatiando e Extendendo Layout

O Blade oferece ferramentas poderosas para criar layouts reutilizáveis e consistentes, permitindo que você divida seu layout em partes menores e os estenda conforme necessário. Isso facilita a manutenção e a organização das suas views.

## Criando um Layout Base

Um layout base serve como um modelo para suas views. Use a diretiva `@yield` para definir seções que serão preenchidas em views específicas.

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

### Partials

Partials são pedaços de código que podem ser incluídos em várias views, como cabeçalhos e rodapés.

#### Exemplo de Partial

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

## Extendendo o Layout

Use a diretiva `@extends` para estender um layout base e a diretiva `@section` para preencher as seções definidas no layout.

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

Seções opcionais permitem definir conteúdo que só será exibido se uma seção específica for preenchida. Use a função `@hasSection` para verificar se uma seção foi preenchida.

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

## Layouts Aninhados

Layouts aninhados permitem que você crie hierarquias de layouts, onde um layout base é estendido por outro layout, que por sua vez pode ser estendido por views específicas.

### Exemplo de Layout Aninhado

Crie um layout base:

```php
<!-- resources/views/layouts/base.blade.php -->
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

Crie um layout que estenda o layout base:

```php
<!-- resources/views/layouts/app.blade.php -->
@extends('layouts.base')

@section('title', 'Aplicação')

@section('content')
    @yield('main-content')
@endsection
```

Crie uma view que estenda o layout `app`:

```php
<!-- resources/views/home.blade.php -->
@extends('layouts.app')

@section('main-content')
    <h2>Bem-vindo à Página Inicial</h2>
    <p>Este é o conteúdo da página inicial.</p>
@endsection
```

## Resumo

Fatiar e estender layouts com Blade permite criar uma estrutura de views reutilizável e consistente. Utilizando diretivas como `@extends`, `@yield`, `@section`, `@hasSection` e criando partials, você pode manter seu código organizado, modular e fácil de manter.
