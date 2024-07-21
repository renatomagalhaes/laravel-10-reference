# Rate Limiting

Rate limiting é uma técnica usada para controlar a quantidade de requisições que um cliente pode fazer a um servidor em um determinado período de tempo. No Laravel, o middleware `throttle` é usado para implementar rate limiting, ajudando a proteger sua aplicação contra abusos e ataques de negação de serviço (DoS).

## Configurando Rate Limiting

### Passo 1: Utilizar o Middleware `throttle`

Você pode aplicar o middleware `throttle` diretamente nas rotas para limitar a taxa de requisições.

#### Exemplo de Aplicação do Middleware `throttle`

```php
Route::middleware('throttle:10,1')->group(function () {
    Route::get('/profile', function () {
        // Esta rota pode ser acessada 10 vezes por minuto
    });

    Route::post('/profile', function () {
        // Esta rota pode ser acessada 10 vezes por minuto
    });
});
```

### Passo 2: Definir Limites de Taxa Personalizados

Você pode definir limites de taxa personalizados usando a função `throttle` em suas rotas.

#### Exemplo de Definição de Limite de Taxa Personalizado

```php
Route::middleware('throttle:customRateLimit')->group(function () {
    Route::get('/api/data', 'ApiController@getData');
});

RateLimiter::for('customRateLimit', function (Request $request) {
    return Limit::perMinute(30)->by($request->ip());
});
```

### Passo 3: Rate Limiting com Chaves Dinâmicas

Você pode aplicar rate limiting com base em chaves dinâmicas, como ID do usuário ou endereço IP.

#### Exemplo de Rate Limiting com Chave Dinâmica

```php
RateLimiter::for('global', function (Request $request) {
    return Limit::perMinute(1000)->by($request->ip());
});

RateLimiter::for('uploads', function (Request $request) {
    return Limit::perMinute(5)->by(optional($request->user())->id ?: $request->ip());
});
```

## Monitorando Rate Limiting

### Acessando Dados de Rate Limiting

Você pode acessar dados de rate limiting, como o número de tentativas restantes e o tempo até a próxima tentativa permitida, utilizando métodos do objeto `Request`.

#### Exemplo de Acesso a Dados de Rate Limiting

```php
Route::middleware('throttle:10,1')->post('/login', function (Request $request) {
    $attempts = $request->get('login')->remaining();
    $retryAfter = $request->get('login')->retryAfter();

    return response()->json([
        'attempts_remaining' => $attempts,
        'retry_after' => $retryAfter
    ]);
});
```

## Rate Limiting em APIs

### Aplicar Rate Limiting em Rotas de API

Você pode aplicar rate limiting em rotas de API para proteger seus endpoints contra abusos.

#### Exemplo de Rate Limiting em Rotas de API

```php
Route::middleware('auth:sanctum', 'throttle:60,1')->get('/user', function (Request $request) {
    return $request->user();
});
```

## Exceções de Rate Limiting

### Definir Exceções de Rate Limiting

Você pode definir exceções para o rate limiting, permitindo que certas rotas ou usuários bypass o limite.

#### Exemplo de Definição de Exceções

```php
RateLimiter::for('uploads', function (Request $request) {
    if ($request->user()->is_admin) {
        return Limit::none();
    }

    return Limit::perMinute(5)->by($request->user()->id);
});
```

## Resumo

Rate limiting é uma técnica essencial para proteger sua aplicação contra abusos e ataques de negação de serviço. O middleware `throttle` do Laravel facilita a implementação de limites de taxa, permitindo que você defina limites personalizados, monitore tentativas e configure exceções conforme necessário. Utilizar rate limiting ajuda a garantir que sua aplicação permaneça segura e responsiva.
