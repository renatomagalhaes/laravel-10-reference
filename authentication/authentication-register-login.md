# Registro e Login

O Laravel fornece ferramentas prontas para a implementação de funcionalidades de registro e login de usuários. Abaixo estão os passos necessários para configurar e personalizar essas funcionalidades.

## Registro de Usuário

### Passo 1: Rotas

O Laravel UI gera automaticamente as rotas necessárias para registro e login. Verifique o arquivo `routes/web.php` para confirmar:

```php
Auth::routes();
```

### Passo 2: Controladores

Os controladores para registro e login são gerados automaticamente. Verifique os controladores `RegisterController` e `LoginController` em `app/Http/Controllers/Auth`.

### Passo 3: Views

As views para registro e login são armazenadas em `resources/views/auth`. Você pode personalizá-las conforme necessário.

#### Exemplo de Formulário de Registro

```html
<!-- resources/views/auth/register.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Register') }}</div>

                <div class="card-body">
                    <form method="POST" action="{{ route('register') }}">
                        @csrf

                        <div class="form-group row">
                            <label for="name" class="col-md-4 col-form-label text-md-right">{{ __('Name') }}</label>

                            <div class="col-md-6">
                                <input id="name" type="text" class="form-control @error('name') is-invalid @enderror" name="name" value="{{ old('name') }}" required autocomplete="name" autofocus>

                                @error('name')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <!-- Adicione outros campos necessários aqui -->

                        <div class="form-group row mb-0">
                            <div class="col-md-6 offset-md-4">
                                <button type="submit" class="btn btn-primary">
                                    {{ __('Register') }}
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

## Login de Usuário

### Passo 1: Rotas

Assim como no registro, as rotas para login são geradas automaticamente com `Auth::routes();`.

### Passo 2: Controlador

O `LoginController` gerencia a autenticação dos usuários. Verifique seu código em `app/Http/Controllers/Auth/LoginController.php`.

### Passo 3: Views

As views para login são armazenadas em `resources/views/auth`. Você pode personalizá-las conforme necessário.

#### Exemplo de Formulário de Login

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

                        <div class="form-group row">
                            <label for="password" class="col-md-4 col-form-label text-md-right">{{ __('Password') }}</label>

                            <div class="col-md-6">
                                <input id="password" type="password" class="form-control @error('password') is-invalid @enderror" name="password" required autocomplete="current-password">

                                @error('password')
                                    <span class="invalid-feedback" role="alert">
                                        <strong>{{ $message }}</strong>
                                    </span>
                                @enderror
                            </div>
                        </div>

                        <!-- Adicione outros campos necessários aqui -->

                        <div class="form-group row mb-0">
                            <div class="col-md-8 offset-md-4">
                                <button type="submit" class="btn btn-primary">
                                    {{ __('Login') }}
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

## Personalizando a Validação

Você pode personalizar as regras de validação no `RegisterController`. Adicione ou modifique as regras conforme necessário.

### Exemplo de Regras de Validação Personalizadas

```php
// app/Http/Controllers/Auth/RegisterController.php

protected function validator(array $data)
{
    return Validator::make($data, [
        'name' => ['required', 'string', 'max:255'],
        'email' => ['required', 'string', 'email', 'max:255', 'unique:users'],
        'password' => ['required', 'string', 'min:8', 'confirmed'],
    ]);
}
```

## Resumo

A configuração de registro e login no Laravel é facilitada pelo scaffolding automático fornecido pelo Laravel UI. Com ajustes mínimos, você pode personalizar as rotas, controladores e views para atender às necessidades específicas do seu aplicativo. Isso inclui a personalização de formulários e regras de validação para garantir uma experiência de usuário robusta e segura.
