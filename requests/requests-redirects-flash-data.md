# Redirecionamentos e Flash Data

Redirecionamentos e flash data são fundamentais para melhorar a experiência do usuário em aplicações web. No Laravel, esses recursos são simples de implementar e oferecem grande flexibilidade para gerenciar a navegação e a exibição de mensagens temporárias.

## Redirecionamentos

### Redirecionando para uma Rota Específica

Você pode redirecionar o usuário para uma rota específica utilizando o helper `redirect`.

### Exemplo de Redirecionamento para uma Rota

```php
Route::post('/submit', function () {
    // Lógica de processamento...

    return redirect('/home');
});
```

### Redirecionando para uma Rota Nomeada

Para redirecionar para uma rota nomeada, utilize o método `route` do helper `redirect`.

```php
Route::post('/submit', function () {
    // Lógica de processamento...

    return redirect()->route('home');
});
```

### Redirecionando com Dados

Você pode redirecionar com dados adicionais utilizando o método `with`.

```php
Route::post('/submit', function () {
    // Lógica de processamento...

    return redirect()->route('home')->with('status', 'Dados processados com sucesso!');
});
```

## Flash Data

Flash data são dados que persistem na sessão apenas para o próximo request. É útil para exibir mensagens temporárias como confirmações de ações.

### Armazenando Flash Data

Use o método `with` para armazenar flash data.

```php
Route::post('/submit', function () {
    // Lógica de processamento...

    return redirect()->route('home')->with('status', 'Dados processados com sucesso!');
});
```

### Exibindo Flash Data nas Views

Para exibir flash data em uma view, você pode usar a diretiva `@if` do Blade.

```blade
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
```

## Redirecionamentos com Inputs Antigos

Se a validação falhar, você pode redirecionar de volta com os dados do formulário para que o usuário não precise preencher tudo novamente.

### Exemplo de Redirecionamento com Inputs Antigos

```php
use Illuminate\Http\Request;

Route::post('/submit', function (Request $request) {
    $validatedData = $request->validate([
        'name' => 'required|max:255',
        'email' => 'required|email',
    ]);

    // Lógica de processamento...

    return redirect()->back()->withInput();
});
```

### Exibindo Inputs Antigos nas Views

Para exibir os inputs antigos nas views, use o helper `old`.

```blade
<input type="text" name="name" value="{{ old('name') }}">
<input type="email" name="email" value="{{ old('email') }}">
```

## Redirecionamentos Condicionais

Você pode redirecionar condicionalmente com base em lógica de negócios.

### Exemplo de Redirecionamento Condicional

```php
Route::post('/submit', function () {
    // Lógica de processamento...

    if ($someCondition) {
        return redirect()->route('success');
    } else {
        return redirect()->route('error');
    }
});
```

## Resumo

Redirecionamentos e flash data são ferramentas poderosas no Laravel para melhorar a experiência do usuário. Eles permitem que você controle a navegação e exiba mensagens temporárias de forma simples e eficiente. Compreender como usar esses recursos corretamente é essencial para o desenvolvimento de aplicações web robustas e interativas.
