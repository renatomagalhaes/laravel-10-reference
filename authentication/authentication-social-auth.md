# Autenticação via Redes Sociais

O Laravel facilita a implementação de autenticação via redes sociais utilizando o pacote Laravel Socialite. Este pacote permite que os usuários façam login na sua aplicação usando suas contas de redes sociais como Facebook, Google, Twitter, entre outras.

## Configurando o Laravel Socialite

### Passo 1: Instalação

Primeiro, instale o pacote Laravel Socialite:

```bash
composer require laravel/socialite
```

### Passo 2: Configuração dos Serviços

Adicione as credenciais das redes sociais ao arquivo `config/services.php`. Você precisará registrar sua aplicação nas plataformas das redes sociais para obter essas credenciais.

#### Exemplo de Configuração do Google

```php
'google' => [
    'client_id' => env('GOOGLE_CLIENT_ID'),
    'client_secret' => env('GOOGLE_CLIENT_SECRET'),
    'redirect' => env('GOOGLE_REDIRECT_URI'),
],
```

### Passo 3: Configuração do .env

Adicione as credenciais ao arquivo `.env`:

```env
GOOGLE_CLIENT_ID=seu-client-id
GOOGLE_CLIENT_SECRET=sua-client-secret
GOOGLE_REDIRECT_URI=https://seu-dominio.com/auth/google/callback
```

## Configurando as Rotas

Defina as rotas para redirecionar o usuário para o provedor de rede social e para receber o callback após a autenticação.

```php
use Laravel\Socialite\Facades\Socialite;

Route::get('/auth/redirect', function () {
    return Socialite::driver('google')->redirect();
});

Route::get('/auth/callback', function () {
    $user = Socialite::driver('google')->user();

    // $user->token
});
```

## Controladores

Crie um controlador para gerenciar a lógica de autenticação via redes sociais.

### Exemplo de Controlador

```php
namespace App\Http\Controllers\Auth;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Laravel\Socialite\Facades\Socialite;
use App\Models\User;
use Illuminate\Support\Facades\Auth;
use Illuminate\Support\Facades\Hash;

class SocialAuthController extends Controller
{
    public function redirectToProvider()
    {
        return Socialite::driver('google')->redirect();
    }

    public function handleProviderCallback()
    {
        $socialUser = Socialite::driver('google')->user();
        $user = User::where('email', $socialUser->getEmail())->first();

        if ($user) {
            Auth::login($user);
        } else {
            $user = User::create([
                'name' => $socialUser->getName(),
                'email' => $socialUser->getEmail(),
                'password' => Hash::make(uniqid()),
            ]);

            Auth::login($user);
        }

        return redirect('/home');
    }
}
```

## Views

Crie botões de login social nas views de autenticação.

### Exemplo de View

```html
<!-- resources/views/auth/login.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Login') }}</div>

                <div class="card-body">
                    <form method="POST" action="{{ route('login') }}">
                        @csrf
                        <!-- Campos de login -->

                        <div class="form-group row">
                            <div class="col-md-8 offset-md-4">
                                <a href="{{ url('/auth/redirect') }}" class="btn btn-primary">
                                    {{ __('Login com Google') }}
                                </a>
                            </div>
                        </div>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

## Resumo

A autenticação via redes sociais no Laravel utilizando o Socialite é um processo direto que envolve a instalação do pacote, configuração das credenciais dos provedores de redes sociais, definição das rotas e criação dos controladores necessários. Isso permite que os usuários façam login na sua aplicação usando suas contas de redes sociais, proporcionando uma experiência de usuário simplificada e segura.
