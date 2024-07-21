# Throttle Customizado

O controle de taxa (rate limiting) é uma técnica usada para limitar o número de requisições que um usuário pode fazer em um determinado período de tempo. No Laravel, você pode personalizar o comportamento do throttle para atender a necessidades específicas da sua aplicação.

## Implementação de Throttle Customizado

### Passo 1: Definir Limites de Taxa Personalizados

Você pode definir limites de taxa personalizados utilizando a classe `RateLimiter` do Laravel. Isso permite uma configuração detalhada baseada em diferentes critérios.

#### Exemplo de Definição de Limite de Taxa Personalizado

```php
use Illuminate\Cache\RateLimiter;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\RateLimiter as RateLimiterFacade;

RateLimiterFacade::for('global', function (Request $request) {
    return $request->user()
                ? Limit::perMinute(100)->by($request->user()->id)
                : Limit::perMinute(10)->by($request->ip());
});
```

### Passo 2: Aplicar o Throttle Customizado nas Rotas

Depois de definir os limites de taxa personalizados, você pode aplicá-los nas rotas utilizando o middleware `throttle`.

```php
Route::middleware('throttle:global')->group(function () {
    Route::get('/profile', function () {
        // Esta rota está sujeita ao throttle global personalizado
    });

    Route::post('/profile', function () {
        // Esta rota está sujeita ao throttle global personalizado
    });
});
```

## Diferentes Limites para Usuários Autenticados e Não Autenticados

### Exemplo de Limites Diferentes

Você pode aplicar diferentes limites de taxa para usuários autenticados e não autenticados.

```php
RateLimiterFacade::for('uploads', function (Request $request) {
    return $request->user()
                ? Limit::perMinute(30)->by($request->user()->id)
                : Limit::perMinute(5)->by($request->ip());
});
```

### Passo 3: Monitorar o Uso do Throttle

Você pode monitorar o uso do throttle para entender melhor o comportamento dos usuários e ajustar os limites conforme necessário.

```php
Route::middleware('throttle:uploads')->post('/upload', function (Request $request) {
    // Lógica de upload de arquivo
});
```

## Implementação de Throttle por Função

Você pode aplicar limites de taxa diferentes para diferentes funções ou roles de usuário.

### Exemplo de Throttle por Função

```php
RateLimiterFacade::for('admin', function (Request $request) {
    return $request->user()->isAdmin()
                ? Limit::perMinute(100)->by($request->user()->id)
                : Limit::perMinute(10)->by($request->ip());
});
```

### Passo 4: Utilizar o Throttle Personalizado

Você pode utilizar o throttle personalizado em suas rotas específicas.

```php
Route::middleware('throttle:admin')->group(function () {
    Route::get('/admin/dashboard', function () {
        // Lógica do painel administrativo
    });

    Route::post('/admin/settings', function () {
        // Lógica de atualização de configurações administrativas
    });
});
```

## Tratamento de Exceções

### Passo 1: Configurar Mensagens Personalizadas

Você pode configurar mensagens de erro personalizadas para quando o limite de taxa for excedido.

```php
use Illuminate\Http\Exceptions\ThrottleRequestsException;

RateLimiterFacade::for('global', function (Request $request) {
    return Limit::perMinute(100)->by($request->user()->id)->response(function () {
        throw new ThrottleRequestsException('Você excedeu o limite de requisições. Por favor, tente novamente mais tarde.');
    });
});
```

## Resumo

O throttle personalizado no Laravel permite que você configure limites de taxa detalhados e específicos para diferentes tipos de usuários, funções e padrões de uso. Isso é essencial para proteger sua aplicação contra abusos, garantindo ao mesmo tempo uma experiência de usuário fluida. Com uma configuração cuidadosa e monitoramento contínuo, você pode ajustar os limites de taxa conforme necessário para otimizar o desempenho e a segurança da sua aplicação.
