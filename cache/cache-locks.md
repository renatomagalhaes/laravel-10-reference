# Cache Locks

Os Cache Locks (bloqueios de cache) no Laravel fornecem uma maneira de garantir que apenas um processo ou tarefa pode executar uma determinada operação por vez. Isso é útil para evitar condições de corrida e garantir a consistência dos dados.

## Introdução aos Cache Locks

Cache Locks permitem que você controle o acesso a recursos compartilhados em sua aplicação, garantindo que apenas um processo possa acessar ou modificar um recurso ao mesmo tempo. Isso é especialmente útil em cenários de concorrência onde múltiplos processos podem tentar executar a mesma operação simultaneamente.

## Criando um Cache Lock

### Passo 1: Criar um Lock

Você pode criar um lock usando o método `lock` do cache:

```php
$lock = Cache::lock('process-name', 10);
```

Neste exemplo, um lock é criado com o nome `process-name` e um tempo de expiração de 10 segundos.

### Passo 2: Adquirir e Liberar o Lock

Para adquirir o lock, você pode usar o método `get`:

```php
if ($lock->get()) {
    // Executar a operação crítica

    // Liberar o lock quando a operação estiver completa
    $lock->release();
} else {
    // O lock não pôde ser adquirido
    return 'Outro processo está executando esta operação.';
}
```

## Cache Locks com Retentativas

### Tentando Adquirir o Lock com Retentativas

Você pode tentar adquirir um lock com retentativas usando o método `block`:

```php
$lock = Cache::lock('process-name', 10);

$lock->block(5, function () {
    // Executar a operação crítica
});
```

Neste exemplo, o método `block` tentará adquirir o lock por até 5 segundos antes de desistir.

## Cache Locks com Redis

### Configuração do Redis para Cache Locks

Certifique-se de que você configurou o Redis como driver de cache no arquivo `.env`:

```env
CACHE_DRIVER=redis
```

### Criando um Lock com Redis

O uso de locks com Redis é o mesmo que com outros drivers de cache:

```php
$lock = Cache::lock('process-name', 10);

if ($lock->get()) {
    // Executar a operação crítica
    $lock->release();
}
```

## Exemplos Práticos

### Sincronizando Tarefas Cron

Você pode usar locks para sincronizar tarefas cron, garantindo que apenas uma instância da tarefa seja executada por vez:

```php
$schedule->call(function () {
    $lock = Cache::lock('cron-task', 60);

    if ($lock->get()) {
        // Executar a tarefa cron
        $lock->release();
    }
})->everyMinute();
```

### Evitando Duplicação de Processamento

Use locks para evitar que múltiplos processos manipulem a mesma tarefa ao mesmo tempo:

```php
class OrderProcessor
{
    public function process(Order $order)
    {
        $lock = Cache::lock('order-processing-' . $order->id, 30);

        if ($lock->get()) {
            // Processar o pedido
            $lock->release();
        } else {
            // O pedido já está sendo processado
            return 'O pedido já está sendo processado.';
        }
    }
}
```

## Resumo

Cache Locks no Laravel fornecem uma maneira eficaz de controlar o acesso a recursos compartilhados, evitando condições de corrida e garantindo a consistência dos dados. Usando locks, você pode sincronizar tarefas e evitar duplicação de processamento, melhorando a robustez e a confiabilidade da sua aplicação.
