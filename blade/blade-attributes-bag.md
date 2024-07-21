# Attributes Bag

O Attributes Bag é uma funcionalidade poderosa do Blade que permite a manipulação flexível de atributos HTML em componentes de Blade. Isso é especialmente útil quando se trabalha com componentes reutilizáveis e altamente configuráveis.

## Uso Básico do Attributes Bag

Os componentes do Blade podem receber atributos adicionais usando o `{{ $attributes }}`.

### Exemplo de Componente com Attributes Bag

Crie um componente chamado `button`:

```php
<!-- resources/views/components/button.blade.php -->
<button {{ $attributes->merge(['class' => 'btn']) }}>
    {{ $slot }}
</button>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-button class="btn-primary">
    Clique aqui
</x-button>
```

### Resultado HTML

```html
<button class="btn btn-primary">
    Clique aqui
</button>
```

## Merge de Atributos

Você pode usar o método `merge` para mesclar os atributos padrão do componente com os atributos fornecidos.

### Exemplo de Merge de Atributos

```php
<!-- resources/views/components/input.blade.php -->
<input {{ $attributes->merge(['class' => 'form-control', 'type' => 'text']) }}>
```

Utilize o componente em uma view:

```php
<!-- resources/views/form.blade.php -->
<x-input class="form-control-lg" placeholder="Digite seu nome">
```

### Resultado HTML

```html
<input class="form-control form-control-lg" type="text" placeholder="Digite seu nome">
```

## Manipulação de Atributos

Você pode adicionar, remover ou modificar atributos dinamicamente usando métodos como `except` e `only`.

### Exemplo de Manipulação de Atributos

```php
<!-- resources/views/components/card.blade.php -->
<div {{ $attributes->except('class')->merge(['class' => 'card']) }}>
    <div class="card-body">
        {{ $slot }}
    </div>
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-card id="main-card" class="custom-card">
    Conteúdo do cartão
</x-card>
```

### Resultado HTML

```html
<div id="main-card" class="card custom-card">
    <div class="card-body">
        Conteúdo do cartão
    </div>
</div>
```

## Definindo Atributos Padrão

Você pode definir atributos padrão que sempre serão aplicados ao componente, a menos que sejam sobrescritos.

### Exemplo de Atributos Padrão

```php
<!-- resources/views/components/alert.blade.php -->
<div {{ $attributes->merge(['class' => 'alert alert-info']) }}>
    {{ $slot }}
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/welcome.blade.php -->
<x-alert class="alert-danger">
    Atenção! Isso é uma mensagem de alerta.
</x-alert>
```

### Resultado HTML

```html
<div class="alert alert-info alert-danger">
    Atenção! Isso é uma mensagem de alerta.
</div>
```

## Resumo

O Attributes Bag é uma ferramenta poderosa no Blade para gerenciar atributos HTML de forma flexível e dinâmica em componentes. Usando métodos como `merge`, `except` e `only`, você pode criar componentes reutilizáveis e configuráveis que se adaptam facilmente às necessidades da sua aplicação.
