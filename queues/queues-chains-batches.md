# Chains e Batches

O Laravel oferece recursos avançados de filas que permitem encadear (chaining) e agrupar (batching) jobs para uma melhor organização e controle do fluxo de trabalho. Este guia aborda como usar chains e batches para gerenciar jobs complexos.

## Encadeamento de Jobs (Chains)

### O que é Chain?

Encadear jobs permite que você defina uma sequência de jobs que devem ser executados em uma ordem específica. Se um job falhar, os jobs subsequentes na cadeia não serão executados.

### Criar Jobs Encadeados

Para criar uma sequência de jobs encadeados, use o método `chain`:

```php
use App\Jobs\FirstJob;
use App\Jobs\SecondJob;

class ExampleController extends Controller
{
    public function dispatchChainedJobs()
    {
        $firstJobData = 'Dados do primeiro job';
        $secondJobData = 'Dados do segundo job';

        FirstJob::withChain([
            new SecondJob($secondJobData),
        ])->dispatch($firstJobData);

        return response()->json(['message' => 'Jobs encadeados despachados com sucesso!']);
    }
}
```

### Exemplo de Jobs

#### Primeiro Job

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class FirstJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function handle()
    {
        // Lógica do primeiro job
    }
}
```

#### Segundo Job

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;

class SecondJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $data;

    public function __construct($data)
    {
        $this->data = $data;
    }

    public function handle()
    {
        // Lógica do segundo job
    }
}
```

## Agrupamento de Jobs (Batches)

### O que é Batch?

Agrupar jobs permite que você execute múltiplos jobs simultaneamente como um grupo e obtenha uma visão geral do progresso e do status de todos os jobs no grupo. Isso é útil para processar grandes volumes de tarefas relacionadas.

### Criar Batches de Jobs

Use o método `batch` para criar um grupo de jobs:

```php
use Illuminate\Bus\Batch;
use Illuminate\Support\Facades\Bus;
use Throwable;

class ExampleController extends Controller
{
    public function dispatchBatchJobs()
    {
        $batch = Bus::batch([
            new FirstJob('Dados do primeiro job'),
            new SecondJob('Dados do segundo job'),
        ])->then(function (Batch $batch) {
            // Todos os jobs foram processados com sucesso...
        })->catch(function (Batch $batch, Throwable $e) {
            // Um ou mais jobs falharam...
        })->finally(function (Batch $batch) {
            // O batch foi concluído...
        })->dispatch();

        return response()->json(['message' => 'Batch de jobs despachado com sucesso!']);
    }
}
```

### Monitorar Progresso de Batches

Para monitorar o progresso de um batch, você pode usar Laravel Horizon ou implementar sua própria lógica de monitoramento. O Horizon fornece uma interface gráfica para visualizar o status dos batches.

## Resumo

O uso de chains e batches no Laravel permite gerenciar tarefas complexas de maneira eficiente. Encadear jobs garante que tarefas dependentes sejam executadas na ordem correta, enquanto agrupar jobs facilita o processamento de grandes volumes de tarefas relacionadas, proporcionando uma visão clara do progresso e do status.
