# Guardiões Personalizados

No Laravel, os guardiões (guards) são usados para definir a forma como os usuários são autenticados para cada solicitação. A criação de guardiões personalizados permite que você personalize o comportamento de autenticação para diferentes partes da sua aplicação.

## Configurando Guardiões Personalizados

### Passo 1: Definir o Guardião no Arquivo de Configuração

Abra o arquivo `config/auth.php` e adicione uma nova configuração de guardião. Por exemplo, vamos criar um guardião chamado `admin`:

```php
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],

    'api' => [
        'driver' => 'token',
        'provider' => 'users',
        'hash' => false,
    ],

    'admin' => [
        'driver' => 'session',
        'provider' => 'admins',
    ],
],

'providers' => [
    'users' => [
        'driver' => 'eloquent',
        'model' => App\Models\User::class,
    ],

    'admins' => [
        'driver' => 'eloquent',
        'model' => App\Models\Admin::class,
    ],
],
```

### Passo 2: Criar o Modelo de Administrador

Crie um modelo Eloquent para o administrador. Este modelo será usado pelo novo guardião.

```bash
php artisan make:model Admin
```

### Passo 3: Configurar as Rotas

Adicione as rotas que utilizarão o guardião personalizado. Por exemplo, rotas protegidas pelo guardião `admin`:

```php
Route::group(['middleware' => ['auth:admin']], function () {
    Route::get('/admin/dashboard', 'AdminController@dashboard')->name('admin.dashboard');
});
```

### Passo 4: Criar o Controlador de Autenticação de Administradores

Crie um controlador para gerenciar a autenticação dos administradores.

```bash
php artisan make:controller AdminAuthController
```

### Exemplo de Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Auth;

class AdminAuthController extends Controller
{
    public function showLoginForm()
    {
        return view('admin.login');
    }

    public function login(Request $request)
    {
        $credentials = $request->only('email', 'password');

        if (Auth::guard('admin')->attempt($credentials)) {
            return redirect()->intended('/admin/dashboard');
        }

        return back()->withErrors([
            'email' => 'As credenciais fornecidas não puderam ser verificadas.',
        ]);
    }

    public function logout(Request $request)
    {
        Auth::guard('admin')->logout();
        $request->session()->invalidate();

        return redirect('/admin/login');
    }
}
```

### Passo 5: Criar as Views de Login

Crie as views para o formulário de login do administrador.

#### Exemplo de Formulário de Login

```html
<!-- resources/views/admin/login.blade.php -->
@extends('layouts.app')

@section('content')
<div class="container">
    <div class="row justify-content-center">
        <div class="col-md-8">
            <div class="card">
                <div class="card-header">{{ __('Admin Login') }}</div>

                <div class="card-body">
                    <form method="POST" action="{{ route('admin.login') }}">
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

## Resumo

Criar guardiões personalizados no Laravel permite que você configure diferentes métodos de autenticação para diferentes partes da sua aplicação. Isso é útil para aplicações que têm diferentes tipos de usuários, como administradores e clientes, que requerem diferentes métodos de autenticação e proteção de rotas.
