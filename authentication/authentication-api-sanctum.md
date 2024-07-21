# Autenticação API com Laravel Sanctum

O Laravel Sanctum oferece um sistema leve de autenticação para SPAs (Single Page Applications), aplicações mobile, e simples APIs. É uma solução prática e segura para gerenciar autenticação baseada em tokens e proteção de rotas de API.

## Configurando o Laravel Sanctum

### Passo 1: Instalação

Primeiro, instale o pacote Laravel Sanctum:

```bash
composer require laravel/sanctum
```

### Passo 2: Publicar a Configuração

Depois, publique o arquivo de configuração do Sanctum:

```bash
php artisan vendor:publish --provider="Laravel\Sanctum\SanctumServiceProvider"
```

### Passo 3: Executar as Migrações

Sanctum precisa criar algumas tabelas no banco de dados. Execute as migrações:

```bash
php artisan migrate
```

### Passo 4: Configurar o Middleware

Adicione o middleware do Sanctum ao grupo de middleware da API no arquivo `app/Http/Kernel.php`:

```php
'api' => [
    \Laravel\Sanctum\Http\Middleware\EnsureFrontendRequestsAreStateful::class,
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
],
```

### Passo 5: Configurar o Sanctum

Adicione o trait `HasApiTokens` ao seu modelo User:

```php
use Laravel\Sanctum\HasApiTokens;

class User extends Authenticatable
{
    use HasApiTokens, Notifiable;
}
```

## Protegendo Rotas de API

Você pode proteger suas rotas de API com o middleware `auth:sanctum`.

### Exemplo de Proteção de Rota

```php
use Illuminate\Http\Request;

Route::middleware('auth:sanctum')->get('/user', function (Request $request) {
    return $request->user();
});
```

## Emitindo Tokens

Para emitir tokens, crie um endpoint de login que gere um token para o usuário autenticado.

### Exemplo de Endpoint de Login

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Hash;
use App\Models\User;

Route::post('/login', function (Request $request) {
    $request->validate([
        'email' => 'required|email',
        'password' => 'required',
    ]);

    $user = User::where('email', $request->email)->first();

    if (! $user || ! Hash::check($request->password, $user->password)) {
        return response()->json(['message' => 'Credenciais inválidas'], 401);
    }

    $token = $user->createToken('token-name')->plainTextToken;

    return response()->json(['token' => $token]);
});
```

## Revogando Tokens

Você pode revogar tokens de um usuário quando necessário.

### Exemplo de Revogação de Token

```php
Route::post('/logout', function (Request $request) {
    $request->user()->currentAccessToken()->delete();
    return response()->json(['message' => 'Desconectado com sucesso']);
})->middleware('auth:sanctum');
```

### Revogando Todos os Tokens

```php
Route::post('/logout-all', function (Request $request) {
    $request->user()->tokens()->delete();
    return response()->json(['message' => 'Desconectado de todos os dispositivos com sucesso']);
})->middleware('auth:sanctum');
```

## Resumo

O Laravel Sanctum é uma ferramenta poderosa e flexível para implementar autenticação em APIs. Ele permite a emissão e revogação de tokens, protege rotas de API, e é fácil de configurar. Com o Sanctum, você pode gerenciar a autenticação em suas aplicações de maneira segura e eficiente.
