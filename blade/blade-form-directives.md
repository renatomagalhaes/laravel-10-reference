# Diretivas em Campos de Formulário

Blade fornece várias diretivas que facilitam a criação e o gerenciamento de formulários. Essas diretivas ajudam a simplificar a criação de formulários, validação e inclusão de CSRF tokens.

## CSRF Protection

A diretiva `@csrf` é usada para gerar um token CSRF (Cross-Site Request Forgery) que deve ser incluído em todos os formulários POST para proteger contra ataques CSRF.

### Exemplo de Uso da Diretiva `@csrf`

```php
<form method="POST" action="/profile">
    @csrf
    <!-- Campos do formulário -->
</form>
```

## Diretiva `@method`

A diretiva `@method` é usada para falsificar métodos HTTP em formulários que suportam apenas GET e POST.

### Exemplo de Uso da Diretiva `@method`

```php
<form method="POST" action="/profile">
    @csrf
    @method('PUT')
    <!-- Campos do formulário -->
</form>
```

## Diretiva `@error`

A diretiva `@error` é usada para exibir mensagens de erro de validação associadas a um campo específico.

### Exemplo de Uso da Diretiva `@error`

```php
<input type="text" name="username">
@error('username')
    <div class="alert alert-danger">{{ $message }}</div>
@enderror
```

## Diretiva `@old`

A diretiva `@old` é usada para preencher campos de formulário com dados antigos após uma submissão inválida.

### Exemplo de Uso da Diretiva `@old`

```php
<input type="text" name="username" value="{{ old('username') }}">
```

## Diretivas de Formulário do Laravel Collective

Para facilitar ainda mais a criação de formulários, você pode utilizar o pacote Laravel Collective. Este pacote oferece um conjunto de helpers para criar formulários de maneira mais simples.

### Exemplo de Uso do Laravel Collective

#### Instalando o Laravel Collective

```bash
composer require laravelcollective/html
```

#### Adicionando o Service Provider e Alias

Adicione o provider e alias no arquivo `config/app.php`.

```php
'providers' => [
    // Outros providers
    Collective\Html\HtmlServiceProvider::class,
],

'aliases' => [
    // Outros aliases
    'Form' => Collective\Html\FormFacade::class,
    'Html' => Collective\Html\HtmlFacade::class,
];
```

#### Criando um Formulário com Laravel Collective

```php
{!! Form::open(['url' => '/profile']) !!}
    {!! Form::label('username', 'Username') !!}
    {!! Form::text('username', old('username')) !!}
    @error('username')
        <div class="alert alert-danger">{{ $message }}</div>
    @enderror
    {!! Form::submit('Save') !!}
{!! Form::close() !!}
```

## Resumo

As diretivas do Blade para campos de formulário tornam a criação e o gerenciamento de formulários mais simples e intuitivos. Utilizando `@csrf`, `@method`, `@error` e `@old`, você pode garantir a segurança e a funcionalidade dos seus formulários. Além disso, o pacote Laravel Collective oferece helpers adicionais para facilitar ainda mais a criação de formulários.
