# Two-Factor Authentication (2FA)

A autenticação de dois fatores (2FA) adiciona uma camada extra de segurança à sua aplicação, exigindo que os usuários forneçam um segundo fator de verificação além da senha. Isso pode incluir um código enviado por SMS, um aplicativo de autenticação ou um e-mail.

## Configurando a Autenticação de Dois Fatores

### Passo 1: Instalação de Pacote

Para adicionar 2FA ao seu aplicativo Laravel, você pode usar um pacote como o `laravel-two-factor-authentication`. Primeiro, instale o pacote:

```bash
composer require pragmarx/google2fa-laravel
```

### Passo 2: Publicar o Arquivo de Configuração

Depois de instalar o pacote, publique o arquivo de configuração:

```bash
php artisan vendor:publish --provider="PragmaRX\Google2FALaravel\ServiceProvider"
```

### Passo 3: Configurar o Middleware

Adicione o middleware 2FA ao grupo de middleware web no arquivo `app/Http/Kernel.php`:

```php
'web' => [
    \App\Http\Middleware\EncryptCookies::class,
    \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
    \Illuminate\Session\Middleware\StartSession::class,
    \Illuminate\View\Middleware\ShareErrorsFromSession::class,
    \App\Http\Middleware\VerifyCsrfToken::class,
    \Illuminate\Routing\Middleware\SubstituteBindings::class,
    \PragmaRX\Google2FALaravel\Middleware::class,
],
```

### Passo 4: Modificar o Modelo de Usuário

Adicione os campos necessários ao modelo de usuário para armazenar a chave secreta 2FA e a data de ativação.

```php
use PragmaRX\Google2FALaravel\Support\Authenticator;

class User extends Authenticatable
{
    protected $fillable = [
        // outros campos
        'google2fa_secret',
    ];

    public function enableTwoFactor()
    {
        $google2fa = app('pragmarx.google2fa');

        $this->google2fa_secret = $google2fa->generateSecretKey();
        $this->save();
    }

    public function disableTwoFactor()
    {
        $this->google2fa_secret = null;
        $this->save();
    }
}
```

### Passo 5: Configurar as Rotas e Controladores

Crie rotas e controladores para gerenciar a configuração do 2FA e a verificação do código.

#### Exemplo de Rotas

```php
Route::get('/2fa/enable', 'TwoFactorController@showEnableForm')->name('2fa.enable');
Route::post('/2fa/enable', 'TwoFactorController@enableTwoFactor')->name('2fa.enable.post');
Route::post('/2fa/disable', 'TwoFactorController@disableTwoFactor')->name('2fa.disable');
Route::get('/2fa/verify', 'TwoFactorController@showVerifyForm')->name('2fa.verify');
Route::post('/2fa/verify', 'TwoFactorController@verifyTwoFactor')->name('2fa.verify.post');
```

#### Exemplo de Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use PragmaRX\Google2FALaravel\Support\Authenticator;

class TwoFactorController extends Controller
{
    public function showEnableForm()
    {
        $google2fa = app('pragmarx.google2fa');
        $user = auth()->user();

        $QR_Image = $google2fa->getQRCodeInline(
            config('app.name'),
            $user->email,
            $user->google2fa_secret
        );

        return view('2fa.enable', ['QR_Image' => $QR_Image, 'secret' => $user->google2fa_secret]);
    }

    public function enableTwoFactor(Request $request)
    {
        $user = auth()->user();
        $user->enableTwoFactor();

        return redirect()->route('2fa.verify');
    }

    public function disableTwoFactor(Request $request)
    {
        $user = auth()->user();
        $user->disableTwoFactor();

        return redirect()->route('home');
    }

    public function showVerifyForm()
    {
        return view('2fa.verify');
    }

    public function verifyTwoFactor(Request $request)
    {
        $user = auth()->user();
        $authenticator = app('pragmarx.google2fa');

        $valid = $authenticator->verifyKey($user->google2fa_secret, $request->input('2fa_code'));

        if ($valid) {
            return redirect()->route('home');
        }

        return redirect()->route('2fa.verify')->withErrors(['2fa_code' => 'The provided 2FA code is invalid.']);
    }
}
```

## Views

Crie as views para permitir que os usuários configurem e verifiquem o 2FA.

### Exemplo de View para Habilitar 2FA

```html
<!-- resources/views/2fa/enable.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Enable Two-Factor Authentication') }}</div>

                <div class="card-body">
                    <p>Scan this QR code with your Google Authenticator app:</p>
                    <div>{!! $QR_Image !!}</div>
                    <p>Or use this secret key: {{ $secret }}</p>

                    <form method="POST" action="{{ route('2fa.enable.post') }}">
                        @csrf
                        <button type="submit" class="btn btn-primary">{{ __('Enable 2FA') }}</button>
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

### Exemplo de View para Verificar 2FA

```html
<!-- resources/views/2fa/verify.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Verify Two-Factor Authentication') }}</div>

                <div class="card-body">
                    <form method="POST" action="{{ route('2fa.verify.post') }}">
                        @csrf

                        <div class="form-group row">
                            <label for="2fa_code" class="col-md-4 col-form-label text-md-right">{{ __('2FA Code') }}</label>

                            <div class="col-md-6">
                                <input id="2fa_code" type="text" class="form-control @error('2fa_code') is-invalid @enderror" name="2fa_code" required>

                                @error('2fa_code')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" class="btn btn-primary">
                                    {{ __('Verify 2FA') }}
                                </button>
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

A autenticação de dois fatores (2FA) aumenta significativamente a segurança da sua aplicação ao exigir uma segunda forma de verificação além da senha. Utilizando pacotes como `pragmarx/google2fa-laravel`, você pode facilmente implementar 2FA no seu aplicativo Laravel, proporcionando uma camada adicional de proteção para seus usuários.
