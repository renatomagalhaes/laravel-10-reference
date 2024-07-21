# Componentes Inline

O Blade permite criar componentes inline, que são componentes definidos diretamente dentro de uma view sem a necessidade de criar um arquivo de componente separado. Isso é útil para componentes simples que não precisam ser reutilizados em várias partes da aplicação.

## Criando Componentes Inline

Componentes inline são definidos usando a diretiva `@component` e encerrados com a diretiva `@endcomponent`.

### Exemplo de Componente Inline

```php
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    @component('components.alert', ['type' => 'danger'])
        @slot('title')
            Erro
        @endslot
        <strong>Atenção!</strong> Algo deu errado.
    @endcomponent
</body>
</html>
```

### Definindo o Componente

```php
<!-- resources/views/components/alert.blade.php -->
<div class="alert alert-{{ $type }}">
    <strong>{{ $title }}</strong> {{ $slot }}
</div>
```

## Passando Dados para Componentes Inline

Você pode passar dados para componentes inline usando a sintaxe de array associativo.

### Exemplo de Passagem de Dados

```php
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    @component('components.alert', ['type' => 'success'])
        @slot('title')
            Sucesso
        @endslot
        <strong>Parabéns!</strong> Sua operação foi concluída com sucesso.
    @endcomponent
</body>
</html>
```

### Definindo o Componente

```php
<!-- resources/views/components/alert.blade.php -->
<div class="alert alert-{{ $type }}">
    <strong>{{ $title }}</strong> {{ $slot }}
</div>
```

## Utilizando Componentes Inline para Estruturas Pequenas

Componentes inline são ideais para estruturas pequenas e simples, onde a criação de um arquivo separado para o componente pode ser desnecessária.

### Exemplo de Componente Inline Simples

```php
<!-- resources/views/welcome.blade.php -->
<!DOCTYPE html>
<html>
<head>
    <title>Minha Aplicação Laravel</title>
</head>
<body>
    @component('components.button', ['url' => 'https://example.com'])
        Clique aqui
    @endcomponent
</body>
</html>
```

### Definindo o Componente

```php
<!-- resources/views/components/button.blade.php -->
<a href="{{ $url }}" class="btn btn-primary">
    {{ $slot }}
</a>
```

## Resumo

Componentes inline no Blade permitem criar componentes diretamente dentro de uma view, sem a necessidade de criar arquivos separados para componentes simples e específicos. Utilizando a diretiva `@component` e passando dados com a sintaxe de array associativo, você pode criar e utilizar componentes de maneira eficiente e organizada.
