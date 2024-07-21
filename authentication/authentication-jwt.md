# Autenticação JWT (JSON Web Token)

A autenticação usando JSON Web Tokens (JWT) é uma abordagem comum para autenticar APIs, proporcionando uma maneira segura e compacta de transmitir informações entre um cliente e um servidor.

## Configurando a Autenticação JWT com o Laravel

### Passo 1: Instalação do Pacote

Para implementar JWT no Laravel, usaremos o pacote `tymon/jwt-auth`. Primeiro, instale o pacote via Composer:

```bash
composer require tymon/jwt-auth
```

### Passo 2: Publicar o Arquivo de Configuração

Depois de instalar o pacote, publique o arquivo de configuração:

```bash
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```

### Passo 3: Gerar a Chave Secreta

Gere a chave secreta JWT que será usada para assinar os tokens:

```bash
php artisan jwt:secret
```

### Passo 4: Configurar o Middleware

Adicione o middleware JWT ao grupo de middleware da API no arquivo `app/Http/Kernel.php`:

```php
'api' => [
    'throttle:api',
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
    \Tymon\JWTAuth\Http\Middleware\Authenticate::class,
],
```

### Passo 5: Configurar as Rotas de Autenticação

Crie rotas para registro, login e logout usando JWT.

#### Exemplo de Rotas

```php
use App\Http\Controllers\AuthController;

Route::group(['prefix' => 'auth'], function () {
    Route::post('register', [AuthController::class, 'register']);
    Route::post('login', [AuthController::class, 'login']);
    Route::post('logout', [AuthController::class, 'logout'])->middleware('auth:api');
    Route::get('user', [AuthController::class, 'me'])->middleware('auth:api');
});
```

### Passo 6: Criar o Controlador de Autenticação

Crie um controlador para gerenciar o registro, login e logout de usuários.

#### Exemplo de Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\User;
use Illuminate\Support\Facades\Hash;
use Illuminate\Support\Facades\Validator;
use Tymon\JWTAuth\Facades\JWTAuth;

class AuthController extends Controller
{
    public function register(Request $request)
    {
        $validator = Validator::make($request->all(), [
            'name' => 'required|string|max:255',
            'email' => 'required|string|email|max:255|unique:users',
            'password' => 'required|string|min:8|confirmed',
        ]);

        if ($validator->fails()) {
            return response()->json($validator->errors(), 400);
        }

        $user = User::create([
            'name' => $request->name,
            'email' => $request->email,
            'password' => Hash::make($request->password),
        ]);

        $token = JWTAuth::fromUser($user);

        return response()->json(compact('user', 'token'), 201);
    }

    public function login(Request $request)
    {
        $credentials = $request->only('email', 'password');

        if (!$token = JWTAuth::attempt($credentials)) {
            return response()->json(['error' => 'Unauthorized'], 401);
        }

        return response()->json(compact('token'));
    }

    public function logout()
    {
        JWTAuth::invalidate(JWTAuth::getToken());

        return response()->json(['message' => 'Successfully logged out']);
    }

    public function me()
    {
        return response()->json(auth()->user());
    }
}
```

## Protegendo Rotas com JWT

Para proteger rotas usando JWT, utilize o middleware `auth:api` nas rotas que devem ser protegidas.

### Exemplo de Proteção de Rota

```php
Route::middleware('auth:api')->get('/profile', function (Request $request) {
    return $request->user();
});
```

## Resumo

A autenticação JWT no Laravel permite criar um sistema seguro e escalável para autenticar APIs. Usando o pacote `tymon/jwt-auth`, você pode implementar registro, login, logout e proteção de rotas de maneira eficiente, garantindo a segurança dos dados transmitidos entre o cliente e o servidor.
