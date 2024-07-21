# Histórico de Emails Enviados e Retentativas

Manter um histórico dos emails enviados e implementar retentativas para emails que falharam são práticas essenciais para garantir a confiabilidade e a auditabilidade do sistema de envio de emails.

## Passo 1: Configurar Tabela de Histórico de Emails

### Criar a Migração

Crie uma migração para a tabela de histórico de emails usando o comando Artisan:

```bash
php artisan make:migration create_email_histories_table
```

### Exemplo de Migração

No arquivo de migração gerado, defina a estrutura da tabela:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmailHistoriesTable extends Migration
{
    public function up()
    {
        Schema::create('email_histories', function (Blueprint $table) {
            $table->id();
            $table->string('recipient');
            $table->string('subject');
            $table->text('body');
            $table->string('status');
            $table->integer('attempts')->default(0);
            $table->timestamp('sent_at')->nullable();
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('email_histories');
    }
}
```

### Executar a Migração

Execute a migração para criar a tabela:

```bash
php artisan migrate
```

## Passo 2: Implementar Histórico de Emails

### Criar o Modelo

Crie um modelo para a tabela de histórico de emails:

```php
php artisan make:model EmailHistory
```

### Exemplo de Modelo

No arquivo `app/Models/EmailHistory.php`, defina o modelo:

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class EmailHistory extends Model
{
    use HasFactory;

    protected $fillable = [
        'recipient', 'subject', 'body', 'status', 'attempts', 'sent_at'
    ];
}
```

### Registrar o Histórico de Emails

No controlador, registre os detalhes do email enviado no histórico:

```php
use App\Models\EmailHistory;
use App\Mail\SimpleMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        $history = EmailHistory::create([
            'recipient' => $details['email'],
            'subject' => $details['subject'],
            'body' => $details['body'],
            'status' => 'pending',
            'attempts' => 0,
        ]);

        try {
            Mail::to($details['email'])->send(new SimpleMail($details));
            $history->update([
                'status' => 'sent',
                'sent_at' => now(),
                'attempts' => $history->attempts + 1,
            ]);
        } catch (\Exception $e) {
            $history->update([
                'status' => 'failed',
                'attempts' => $history->attempts + 1,
            ]);
        }

        return response()->json(['message' => 'Processo de envio de email concluído']);
    }
}
```

## Passo 3: Implementar Retentativas

### Job de Envio de Emails com Retentativas

Use o comando Artisan para criar um job para envio de emails com retentativas:

```bash
php artisan make:job SendEmailWithRetryJob
```

### Exemplo de Job

No arquivo `app/Jobs/SendEmailWithRetryJob.php`, defina a lógica para envio e retentativas:

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Mail;
use App\Mail\SimpleMail;
use App\Models\EmailHistory;

class SendEmailWithRetryJob implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $details;
    protected $history;

    public function __construct($details, EmailHistory $history)
    {
        $this->details = $details;
        $this->history = $history;
    }

    public function handle()
    {
        try {
            Mail::to($this->details['email'])->send(new SimpleMail($this->details));
            $this->history->update([
                'status' => 'sent',
                'sent_at' => now(),
                'attempts' => $this->history->attempts + 1,
            ]);
        } catch (\Exception $e) {
            if ($this->history->attempts < 3) {
                $this->release(60); // Reenvia o job após 60 segundos
            } else {
                $this->history->update([
                    'status' => 'failed',
                    'attempts' => $this->history->attempts + 1,
                ]);
            }
        }
    }
}
```

### Enfileirar o Job

No controlador, enfileire o job para envio de emails com retentativas:

```php
use App\Jobs\SendEmailWithRetryJob;

class EmailController extends Controller
{
    public function sendWithRetry(Request $request)
    {
        $details = $request->validate([
            'email' => 'required|email',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        $history = EmailHistory::create([
            'recipient' => $details['email'],
            'subject' => $details['subject'],
            'body' => $details['body'],
            'status' => 'pending',
            'attempts' => 0,
        ]);

        SendEmailWithRetryJob::dispatch($details, $history);

        return response()->json(['message' => 'Email enfileirado para envio com retentativas']);
    }
}
```

## Benefícios do Histórico e Retentativas

- **Auditabilidade**: Manter um registro de todos os emails enviados, incluindo aqueles que falharam.
- **Confiabilidade**: Implementar retentativas aumenta a chance de entrega dos emails.
- **Diagnóstico de Problemas**: Ajuda a identificar e solucionar problemas relacionados ao envio de emails.

## Resumo

Manter um histórico de emails enviados e implementar retentativas são práticas essenciais para garantir a confiabilidade e a auditabilidade do sistema de envio de emails. Utilizando modelos, jobs e filas, você pode gerenciar eficientemente o envio de emails e garantir que as mensagens sejam entregues, mesmo em caso de falhas temporárias.
