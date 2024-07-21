# Recuperação de Senha

O Laravel facilita a implementação do processo de recuperação de senha através de seu sistema de autenticação integrado. Isso inclui o envio de e-mails de redefinição de senha e a criação de formulários para que os usuários possam redefinir suas senhas.

## Configurando a Recuperação de Senha

### Passo 1: Configuração de E-mail

Certifique-se de que suas configurações de e-mail estejam corretas no arquivo `.env`. Por exemplo, para usar o SMTP do Gmail:

```env
MAIL_MAILER=smtp
MAIL_HOST=smtp.gmail.com
MAIL_PORT=587
MAIL_USERNAME=seu-email@gmail.com
MAIL_PASSWORD=sua-senha
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=seu-email@gmail.com
MAIL_FROM_NAME="${APP_NAME}"
```

### Passo 2: Rotas

As rotas para recuperação de senha são geradas automaticamente pelo Laravel UI. Verifique o arquivo `routes/web.php` para confirmar:

```php
Auth::routes(['verify' => true]);
```

### Passo 3: Views

As views para recuperação de senha são armazenadas em `resources/views/auth/passwords`. Você pode personalizá-las conforme necessário.

#### Exemplo de Formulário de Solicitação de Redefinição de Senha

```html
<!-- resources/views/auth/passwords/email.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Reset Password') }}</div>

                <div class="card-body">
                    @if (session('status'))
                        <div class="alert alert-success" role="alert">
                            {{ session('status') }}
                        </div>
                    @endif

                    <form method="POST" action="{{ route('password.email') }}">
                        @csrf

                        <div class="form-group row">
                            <label for="email" class="col-md-4 col-form-label text-md-right">{{ __('E-Mail Address') }}</label>

                            <div class="col-md-6">
                                <input id="email" type="email" class="form-control @error('email') is-invalid @enderror" name="email" value="{{ old('email') }}" required autocomplete="email" autofocus>

                                @error('email')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" class="btn btn-primary">
                                    {{ __('Send Password Reset Link') }}
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

#### Exemplo de Formulário de Redefinição de Senha

```html
<!-- resources/views/auth/passwords/reset.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Reset Password') }}</div>

                <div class="card-body">
                    <form method="POST" action="{{ route('password.update') }}">
                        @csrf

                        <input type="hidden" name="token" value="{{ $token }}">

                        <div class="form-group row">
                            <label for="email" class="col-md-4 col-form-label text-md-right">{{ __('E-Mail Address') }}</label>

                            <div class="col-md-6">
                                <input id="email" type="email" class="form-control @error('email') is-invalid @enderror" name="email" value="{{ $email ?? old('email') }}" required autocomplete="email" autofocus>

                                @error('email')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="password" class="col-md-4 col-form-label text-md-right">{{ __('Password') }}</label>

                            <div class="col-md-6">
                                <input id="password" type="password" class="form-control @error('password') is-invalid @enderror" name="password" required autocomplete="new-password">

                                @error('password')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <div class="form-group row">
                            <label for="password-confirm" class="col-md-4 col-form-label text-md-right">{{ __('Confirm Password') }}</label>

                            <div class="col-md-6">
                                <input id="password-confirm" type="password" class="form-control" name="password_confirmation" required autocomplete="new-password">
                            </div>
                        </div>

                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" class="btn btn-primary">
                                    {{ __('Reset Password') }}
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

### Passo 4: Configuração Adicional

Você pode personalizar o e-mail de recuperação de senha editando o markdown em `resources/views/vendor/notifications/email.blade.php`. Certifique-se de publicar os arquivos de notificações primeiro, se necessário:

```bash
php artisan vendor:publish --tag=laravel-notifications
```

### Exemplo de Personalização de E-mail

```php
<!-- resources/views/vendor/notifications/email.blade.php -->
@component('mail::message')
# Reset Password Notification

You are receiving this email because we received a password reset request for your account.

@component('mail::button', ['url' => $url])
Reset Password
@endcomponent

If you did not request a password reset, no further action is required.

Thanks,<br>
{{ config('app.name') }}
@endcomponent
```

## Resumo

A configuração de recuperação de senha no Laravel inclui a criação de formulários para solicitar e redefinir senhas, bem como o envio de e-mails de recuperação de senha. Com as ferramentas e configurações fornecidas pelo Laravel, você pode implementar facilmente um sistema de recuperação de senha robusto e seguro.
