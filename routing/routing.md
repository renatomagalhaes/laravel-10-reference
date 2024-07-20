# Sistema de Rotas no Laravel 10

O sistema de rotas do Laravel é um dos componentes mais importantes e flexíveis do framework. Ele permite definir endpoints para responder a diferentes requisições HTTP de forma simples e organizada.

## Definindo Rotas

As rotas no Laravel são definidas nos arquivos localizados no diretório `routes`. Por padrão, há arquivos separados para rotas web (`web.php`) e rotas de API (`api.php`).

### Exemplo de Rota Básica

```php
// routes/web.php

use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return view('welcome');
});
```

### Tipos de Rotas

O Laravel suporta todos os métodos HTTP comuns, como `GET`, `POST`, `PUT`, `PATCH`, `DELETE` e `OPTIONS`.

```php
Route::get('/users', 'UserController@index');    // Rota GET
Route::post('/users', 'UserController@store');   // Rota POST
Route::put('/users/{id}', 'UserController@update');  // Rota PUT
Route::delete('/users/{id}', 'UserController@destroy'); // Rota DELETE
```

## Parâmetros de Rotas

As rotas podem conter parâmetros dinâmicos que são capturados e passados para o controlador.

### Parâmetros Obrigatórios

```php
Route::get('/user/{id}', function ($id) {
    return 'User '.$id;
});
```

### Parâmetros Opcionais

```php
Route::get('/user/{name?}', function ($name = null) {
    return $name;
});
```

## Rotas Nomeadas

Rotas nomeadas permitem gerar URLs ou redirecionar para rotas específicas de forma mais conveniente.

```php
Route::get('/user/profile', 'UserProfileController@show')->name('profile');

$url = route('profile');
```

## Middleware

Middleware fornece um mecanismo conveniente para filtrar requisições HTTP que entram na aplicação. Eles podem ser atribuídos a rotas individuais ou a grupos de rotas.

```php
Route::get('/admin', function () {
    // Ação
})->middleware('auth');
```

## Agrupamento de Rotas

Agrupar rotas permite compartilhar atributos como middleware ou namespaces entre um grande número de rotas sem a necessidade de defini-los individualmente para cada rota.

```php
Route::prefix('admin')->group(function () {
    Route::get('/users', function () {
        // Ação
    });

    Route::get('/settings', function () {
        // Ação
    });
});
```

### Namespace

```php
Route::namespace('Admin')->group(function () {
    // Controladores dentro do namespace "App\Http\Controllers\Admin"
    Route::get('/dashboard', 'DashboardController@index');
});
```

## Restrições de Expressão Regular

Você pode definir restrições de expressão regular nos parâmetros das rotas para garantir que eles sigam um padrão específico.

```php
Route::get('/user/{name}', function ($name) {
    // Ação
})->where('name', '[A-Za-z]+');

Route::get('/user/{id}', function ($id) {
    // Ação
})->where('id', '[0-9]+');
```

## Rotas de API

Laravel facilita a criação de rotas para APIs, utilizando o arquivo routes/api.php.

### Exemplo de Rota de API

```php
// routes/api.php

use Illuminate\Support\Facades\Route;

Route::get('/products', 'ProductController@index');
Route::post('/products', 'ProductController@store');
Route::put('/products/{id}', 'ProductController@update');
Route::delete('/products/{id}', 'ProductController@destroy');
```

## Segurança com Rate Limiting

Rate limiting é uma técnica importante para proteger sua aplicação contra abusos, limitando o número de requisições permitidas em um determinado período de tempo.

### Exemplo de Rate Limiting

```php
// App\Providers\RouteServiceProvider.php

use Illuminate\Cache\RateLimiting\Limit;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter;

public function boot()
{
    $this->configureRateLimiting();

    $this->routes(function () {
        Route::prefix('api')
            ->middleware('api')
            ->namespace($this->namespace)
            ->group(base_path('routes/api.php'));
    });
}

protected function configureRateLimiting()
{
    RateLimiter::for('api', function (Request $request) {
        return Limit::perMinute(60)->by(optional($request->user())->id ?: $request->ip());
    });
}
```

### Personalizando a Resposta de Rate Limit

```php
RateLimiter::for('global', function (Request $request) {
    return Limit::perMinute(1000)->response(function (Request $request, array $headers) {
        return response('Custom response...', 429, $headers);
    });
});
```

### Segmentando Rate Limits

```php
RateLimiter::for('uploads', function (Request $request) {
    return $request->user()->vipCustomer()
                ? Limit::none()
                : Limit::perMinute(100)->by($request->ip());
});
```

## Versionamento de API

Para gerenciar diferentes versões de sua API, você pode prefixar suas rotas de API com o número da versão.

### Exemplo de Versionamento de API

```php
Route::prefix('v1')->group(function () {
    Route::get('/users', 'Api\V1\UserController@index');
    Route::post('/users', 'Api\V1\UserController@store');
});

Route::prefix('v2')->group(function () {
    Route::get('/users', 'Api\V2\UserController@index');
    Route::post('/users', 'Api\V2\UserController@store');
});
```

## Recursos

Para mais informações sobre o sistema de rotas no Laravel, consulte a documentação oficial:
- [Documentação Oficial do Laravel](https://laravel.com/docs/10.x/routing)
- [Rate Limiting](https://laravel.com/docs/10.x/routing#rate-limiting)
- [Versionamento de API](https://laravel.com/docs/10.x/routing#api-resource-routing)
