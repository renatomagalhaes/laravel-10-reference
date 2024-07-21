# Proteção de Rotas

Proteger rotas é uma parte essencial da segurança em qualquer aplicação web. No Laravel, isso é feito principalmente utilizando middlewares que garantem que apenas usuários autenticados ou autorizados possam acessar certas rotas.

## Middleware de Autenticação

O Laravel inclui o middleware `auth` por padrão para proteger rotas que requerem autenticação. Este middleware verifica se o usuário está autenticado antes de permitir o acesso à rota.

### Exemplo de Uso do Middleware `auth`

Para proteger uma rota, adicione o middleware `auth` à rota no arquivo `routes/web.php`:

```php
Route::get('/profile', function () {
    // Somente usuários autenticados podem acessar esta rota
})->middleware('auth');
```

## Middleware de Verificação de E-mail

Para rotas que exigem que o usuário tenha um e-mail verificado, use o middleware `verified`.

### Exemplo de Uso do Middleware `verified`

```php
Route::get('/profile', function () {
    // Somente usuários com e-mail verificado podem acessar esta rota
})->middleware(['auth', 'verified']);
```

## Middleware de Autorização

Para proteger rotas com base em permissões ou roles específicas, utilize gates e policies juntamente com o middleware `can`.

### Exemplo de Uso do Middleware `can`

```php
Route::get('/admin', function () {
    // Somente usuários autorizados podem acessar esta rota
})->middleware('can:access-admin');
```

## Proteger Rotas de API com Laravel Sanctum

Para proteger rotas de API, utilize o middleware `auth:sanctum`. Isso garante que apenas usuários autenticados via token possam acessar as rotas.

### Exemplo de Proteção de Rotas de API

```php
Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```

## Proteger Rotas com Múltiplos Middlewares

Você pode proteger rotas utilizando múltiplos middlewares em conjunto.

### Exemplo de Múltiplos Middlewares

```php
Route::get('/dashboard', function () {
    // Somente usuários autenticados e com e-mail verificado podem acessar esta rota
})->middleware(['auth', 'verified', 'can:view-dashboard']);
```

## Resumo

Proteger rotas no Laravel é simples e flexível graças aos middlewares. Utilizando middlewares como `auth`, `verified` e `can`, você pode garantir que apenas usuários autenticados e autorizados possam acessar certas partes da sua aplicação, aumentando significativamente a segurança e a robustez da aplicação.
