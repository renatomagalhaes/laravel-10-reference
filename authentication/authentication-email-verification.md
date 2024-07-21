# Verificação de E-mail

O Laravel inclui suporte para verificação de e-mails para novos usuários registrados, garantindo que eles forneçam um endereço de e-mail válido antes de permitir o acesso total à aplicação.

## Configurando a Verificação de E-mail

### Passo 1: Rotas

Verifique se as rotas de verificação de e-mail estão ativadas em `routes/web.php`:

```php
Auth::routes(['verify' => true]);
```

### Passo 2: Middleware

O middleware `verified` é usado para proteger rotas que exigem verificação de e-mail. Adicione o middleware a qualquer rota que deva estar protegida:

```php
Route::get('/profile', function () {
    // Somente usuários com e-mail verificado podem acessar esta rota
})->middleware('verified');
```

### Passo 3: Controladores

O `VerifyEmailController` lida com a verificação de e-mails. Você pode encontrar este controlador em `app/Http/Controllers/Auth/VerifyEmailController.php`.

### Passo 4: Views

As views para verificação de e-mail são armazenadas em `resources/views/auth`. Você pode personalizá-las conforme necessário.

#### Exemplo de Página de Solicitação de Verificação

```html
<!-- resources/views/auth/verify.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Verify Your Email Address') }}</div>

                <div class="card-body">
                    @if (session('resent'))
                        <div class="alert alert-success" role="alert">
                            {{ __('A fresh verification link has been sent to your email address.') }}
                        </div>
                    @endif

                    {{ __('Before proceeding, please check your email for a verification link.') }}
                    {{ __('If you did not receive the email') }},
                    <form class="d-inline" method="POST" action="{{ route('verification.resend') }}">
                        @csrf
                        <button type="submit" class="btn btn-link p-0 m-0 align-baseline">{{ __('click here to request another') }}</button>.
                    </form>
                </div>
            </div>
        </div>
    </div>
</div>
@endsection
```

### Passo 5: Configuração de E-mail

Certifique-se de que suas configurações de e-mail estejam corretas no arquivo `.env`, conforme descrito anteriormente.

### Passo 6: Enviando E-mails de Verificação

Laravel automaticamente envia um e-mail de verificação quando um novo usuário é registrado. O conteúdo do e-mail pode ser personalizado em `resources/views/vendor/notifications/email.blade.php`.

### Exemplo de Personalização de E-mail de Verificação

```php
<!-- resources/views/vendor/notifications/email.blade.php -->
@component('mail::message')
# Verify Your Email Address

Please click the button below to verify your email address.

@component('mail::button', ['url' => $url])
Verify Email Address
@endcomponent

If you did not create an account, no further action is required.

Thanks,<br>
{{ config('app.name') }}
@endcomponent
```

## Resumo

A configuração de verificação de e-mail no Laravel envolve a ativação de rotas, o uso de middleware para proteger rotas que exigem verificação, e a personalização das views e e-mails de verificação. Isso garante que apenas usuários com endereços de e-mail válidos tenham acesso completo à aplicação.
