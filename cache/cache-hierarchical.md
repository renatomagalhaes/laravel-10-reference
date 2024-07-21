# Cache Hierárquico

O cache hierárquico é uma técnica avançada que permite implementar múltiplos níveis de cache para melhorar ainda mais a performance e a resiliência da aplicação. Este guia aborda como configurar e usar o cache hierárquico no Laravel.

## Introdução ao Cache Hierárquico

### O que é Cache Hierárquico?

Cache hierárquico envolve a implementação de diferentes níveis de cache, onde cada nível armazena dados com diferentes tempos de vida e escopos. Por exemplo, um nível pode armazenar dados em memória local para acesso rápido, enquanto outro nível pode usar um cache distribuído para armazenamento mais persistente.

### Benefícios do Cache Hierárquico

- **Melhor Performance:** Acesso mais rápido aos dados, já que os níveis de cache mais próximos são consultados primeiro.
- **Maior Resiliência:** Se um nível de cache falhar, os níveis superiores ainda podem fornecer os dados.
- **Eficiência de Recursos:** Redução da carga nos níveis de cache mais persistentes, pois os níveis inferiores atendem a maioria das requisições.

## Configuração de Cache Hierárquico no Laravel

### Passo 1: Configurar Múltiplos Drivers de Cache

Configure múltiplos drivers de cache no arquivo `config/cache.php`:

```php
'stores' => [
    'file' => [
        'driver' => 'file',
        'path' => storage_path('framework/cache/data'),
    ],

    'redis' => [
        'driver' => 'redis',
        'connection' => 'cache',
    ],

    'memcached' => [
        'driver' => 'memcached',
        'persistent_id' => env('MEMCACHED_PERSISTENT_ID'),
        'sasl' => [
            env('MEMCACHED_USERNAME'),
            env('MEMCACHED_PASSWORD'),
        ],
        'options' => [
            // Opcional: Configurações específicas do Memcached
        ],
        'servers' => [
            [
                'host' => env('MEMCACHED_HOST', '127.0.0.1'),
                'port' => env('MEMCACHED_PORT', 11211),
                'weight' => 100,
            ],
        ],
    ],
],
```

### Passo 2: Implementar Lógica de Cache Hierárquico

Implemente a lógica para consultar múltiplos níveis de cache. Se o dado não for encontrado em um nível, ele consulta o próximo nível.

```php
public function getFromCache($key)
{
    // Primeiro nível: cache local (exemplo: Memcached)
    if (Cache::store('memcached')->has($key)) {
        return Cache::store('memcached')->get($key);
    }

    // Segundo nível: cache distribuído (exemplo: Redis)
    if (Cache::store('redis')->has($key)) {
        $value = Cache::store('redis')->get($key);
        
        // Armazena no cache local para futuras requisições
        Cache::store('memcached')->put($key, $value, 600); // 10 minutos
        
        return $value;
    }

    // Dados não encontrados em nenhum nível
    return null;
}
```

### Passo 3: Armazenar Dados no Cache Hierárquico

Armazene os dados nos níveis de cache apropriados:

```php
public function putInCache($key, $value)
{
    // Armazena no cache local
    Cache::store('memcached')->put($key, $value, 600); // 10 minutos

    // Armazena no cache distribuído
    Cache::store('redis')->put($key, $value, 3600); // 1 hora
}
```

## Exemplos Práticos

### Sincronização de Cache

Certifique-se de sincronizar os dados entre os diferentes níveis de cache para garantir a consistência:

```php
public function syncCache($key, $value)
{
    Cache::store('memcached')->put($key, $value, 600); // 10 minutos
    Cache::store('redis')->put($key, $value, 3600); // 1 hora
}
```

### Cache de Resultados de Consultas

Implemente cache hierárquico para armazenar resultados de consultas complexas:

```php
$results = Cache::remember('complex-query', 60, function () {
    return DB::table('large_table')->where('condition', true)->get();
});

$this->syncCache('complex-query', $results);
```

## Resumo

O cache hierárquico é uma técnica poderosa para melhorar a performance e a resiliência da sua aplicação Laravel. Implementando múltiplos níveis de cache, você pode garantir acesso rápido aos dados e maior eficiência no uso de recursos. Com a configuração e a lógica adequadas, sua aplicação se beneficiará de tempos de resposta mais rápidos e maior estabilidade.
