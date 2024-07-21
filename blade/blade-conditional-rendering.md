# Renderização Condicional com Blade Components

Componentes do Blade permitem a renderização condicional, o que é útil para criar layouts dinâmicos e complexos. A combinação da diretiva `@component` com a função `@props` permite controlar a renderização com base em condições dinâmicas.

## Exemplo de Renderização Condicional

Crie um componente chamado `status-message`:

```php
<!-- resources/views/components/status-message.blade.php -->
<div>
    @if ($status === 'success')
        <div class="alert alert-success">Operação bem-sucedida!</div>
    @elseif ($status === 'error')
        <div class="alert alert-danger">Ocorreu um erro!</div>
    @else
        <div class="alert alert-info">Status desconhecido.</div>
    @endif
</div>
```

Utilize o componente em uma view:

```php
<!-- resources/views/home.blade.php -->
<x-status-message status="success"/>
<x-status-message status="error"/>
<x-status-message status="unknown"/>
```

### Resultado HTML

```html
<div class="alert alert-success">Operação bem-sucedida!</div>
<div class="alert alert-danger">Ocorreu um erro!</div>
<div class="alert alert-info">Status desconhecido.</div>
```

## Resumo

Utilizar componentes do Blade para renderização condicional permite criar layouts dinâmicos e flexíveis. Isso facilita a criação de interfaces complexas que respondem dinamicamente às condições dos dados fornecidos, tornando os componentes mais reutilizáveis e eficientes.
