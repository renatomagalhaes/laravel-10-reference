# Autorização em Vistas

A autorização em vistas permite controlar a exibição de elementos de interface com base nas permissões dos usuários. No Laravel, você pode usar diretivas Blade para condicionalmente exibir ou ocultar partes da interface de usuário.

## Introdução à Autorização em Vistas

### O que é Autorização em Vistas?

A autorização em vistas é o processo de verificar permissões dos usuários diretamente nas views Blade, permitindo que você condicionalmente exiba ou oculte elementos da interface com base nas permissões do usuário.

### Benefícios da Autorização em Vistas

- **Segurança:** Garante que apenas usuários autorizados possam ver elementos sensíveis da interface.
- **Usabilidade:** Melhora a experiência do usuário ao mostrar apenas o que é relevante para ele.
- **Flexibilidade:** Permite uma interface dinâmica e adaptável baseada nas permissões do usuário.

## Usando Diretivas Blade

### Diretiva @can

A diretiva `@can` verifica se o usuário está autorizado a realizar uma determinada ação:

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@endcan
```

### Diretiva @cannot

A diretiva `@cannot` verifica se o usuário não está autorizado a realizar uma determinada ação:

```blade
@cannot('update', $post)
    <p>Você não tem permissão para editar este post.</p>
@endcannot
```

### Diretiva @role

Se você estiver usando autorização baseada em funções, pode usar a diretiva `@role`:

```blade
@role('admin')
    <a href="{{ route('admin.dashboard') }}">Dashboard Admin</a>
@endrole
```

### Diretiva @else

Você pode combinar diretivas Blade com `@else` para fornecer um comportamento alternativo:

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@else
    <p>Você não tem permissão para editar este post.</p>
@endcan
```

## Exibindo Conteúdo com Base em Várias Permissões

### Usando Múltiplas Regras de Autorização

Você pode verificar múltiplas permissões usando `@can` em combinação com `@elsecan`:

```blade
@can('view', $post)
    <a href="{{ route('posts.show', $post) }}">Visualizar</a>
@elsecan('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@else
    <p>Você não tem permissão para visualizar ou editar este post.</p>
@endcan
```

### Exemplo de Exibição Condicional de Botões

```blade
@can('delete', $post)
    <form action="{{ route('posts.destroy', $post) }}" method="POST">
        @csrf
        @method('DELETE')
        <button type="submit">Deletar</button>
    </form>
@endcan
```

## Exibindo Conteúdo com Base em Gates

### Usando Gates em Vistas

Você pode usar gates diretamente nas views Blade para verificar permissões específicas:

```blade
@can('view-dashboard')
    <a href="{{ route('dashboard') }}">Dashboard</a>
@endcan
```

### Definindo Gates

Certifique-se de que os gates estão definidos no `AuthServiceProvider`:

```php
Gate::define('view-dashboard', function ($user) {
    return $user->hasRole('admin');
});
```

## Exemplos Práticos

### Exemplo de Autorização de Postagens

#### Diretiva @can

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@endcan
```

#### Diretiva @role

```blade
@role('admin')
    <a href="{{ route('admin.dashboard') }}">Dashboard Admin</a>
@endrole
```

#### Diretiva @else

```blade
@can('update', $post)
    <a href="{{ route('posts.edit', $post) }}">Editar</a>
@else
    <p>Você não tem permissão para editar este post.</p>
@endcan
```

## Resumo

A autorização em vistas no Laravel permite controlar a exibição de elementos da interface com base nas permissões dos usuários. Usando diretivas Blade como `@can`, `@cannot`, `@role` e `@else`, você pode criar interfaces dinâmicas e seguras que melhoram a experiência do usuário e garantem que apenas usuários autorizados possam acessar recursos sensíveis.
