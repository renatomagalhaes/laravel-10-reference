# Slots

Slots são uma funcionalidade poderosa do Blade que permitem passar conteúdo dinâmico para dentro de componentes. Eles são especialmente úteis para criar componentes reutilizáveis que podem receber diferentes conteúdos em diferentes contextos.

## Slots Simples

Um slot simples é um espaço reservado em um componente onde o conteúdo dinâmico será inserido.

### Exemplo de Uso de Slots Simples

Crie um componente chamado `alert`:

```php
<!-- resources/views/components/alert.blade.php -->
<div class="alert alert-{{ $type }}">
    {{ $slot }}
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-alert type="danger">
    Atenção! Isso é uma mensagem de alerta.
</x-alert>
```

### Resultado HTML

```html
<div class="alert alert-danger">
    Atenção! Isso é uma mensagem de alerta.
</div>
```

## Slots Nomeados

Slots nomeados permitem definir múltiplos espaços reservados dentro de um componente, cada um com um nome específico.

### Exemplo de Uso de Slots Nomeados

Crie um componente chamado `card`:

```php
<!-- resources/views/components/card.blade.php -->
<div class="card">
    <div class="card-header">
        {{ $header }}
    </div>
    <div class="card-body">
        {{ $slot }}
    </div>
    <div class="card-footer">
        {{ $footer }}
    </div>
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-card>
    <x-slot name="header">
        Cabeçalho do Cartão
    </x-slot>
    Conteúdo do cartão
    <x-slot name="footer">
        Rodapé do Cartão
    </x-slot>
</x-card>
```

### Resultado HTML

```html
<div class="card">
    <div class="card-header">
        Cabeçalho do Cartão
    </div>
    <div class="card-body">
        Conteúdo do cartão
    </div>
    <div class="card-footer">
        Rodapé do Cartão
    </div>
</div>
```

## Slots Padrão

Você pode definir conteúdo padrão para slots que será exibido se nenhum conteúdo for fornecido.

### Exemplo de Uso de Slots Padrão

Crie um componente chamado `message`:

```php
<!-- resources/views/components/message.blade.php -->
<div class="message">
    {{ $slot ?? 'Mensagem padrão' }}
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-message>
    Olá, esta é uma mensagem personalizada.
</x-message>

<x-message />
```

### Resultado HTML

```html
<div class="message">
    Olá, esta é uma mensagem personalizada.
</div>

<div class="message">
    Mensagem padrão
</div>
```

## Resumo

Slots no Blade oferecem uma maneira flexível e poderosa de passar conteúdo dinâmico para dentro de componentes. Usando slots simples, slots nomeados e slots padrão, você pode criar componentes reutilizáveis e altamente configuráveis que se adaptam facilmente às necessidades de sua aplicação.
