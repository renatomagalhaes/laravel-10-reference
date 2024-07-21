# Trabalhando com Múltiplos Templates de Email para Campanhas Automatizadas

Gerenciar múltiplos templates de email é essencial para campanhas de marketing automatizadas, onde diferentes templates podem ser utilizados para diversos fins, como promoções, newsletters e atualizações. Implementar uma solução que permita a criação, armazenamento e envio programado de emails usando templates no Laravel é um processo que envolve a criação de modelos, tabelas no banco de dados e jobs agendados.

## Passo 1: Configurar a Estrutura do Projeto

### Definir o Modelo de Template de Email

Crie um modelo para representar os templates de email na sua aplicação. Este modelo armazenará as informações de cada template de email.

\```php
php artisan make:model EmailTemplate
\```

### Exemplo de Modelo de Template de Email

No arquivo `app/Models/EmailTemplate.php`, defina o modelo:

\```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class EmailTemplate extends Model
{
    use HasFactory;

    protected $fillable = [
        'name', 'subject', 'body', 'status'
    ];
}
\```

### Criar a Migração

Crie uma migração para a tabela de templates de email usando o comando Artisan:

\```bash
php artisan make:migration create_email_templates_table
\```

### Exemplo de Migração

No arquivo de migração gerado, defina a estrutura da tabela:

\```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmailTemplatesTable extends Migration
{
    public function up()
    {
        Schema::create('email_templates', function (Blueprint $table) {
            $table->id();
            $table->string('name');
            $table->string('subject');
            $table->text('body');
            $table->string('status')->default('active'); // active, inactive
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('email_templates');
    }
}
\```

### Executar a Migração

Execute a migração para criar a tabela:

\```bash
php artisan migrate
\```

## Passo 2: Gerenciar Templates de Email

### Criar Controlador para Gerenciar Templates

Use o comando Artisan para criar um controlador para gerenciar templates de email:

\```bash
php artisan make:controller EmailTemplateController
\```

### Exemplo de Controlador

No arquivo `app/Http/Controllers/EmailTemplateController.php`, defina os métodos para criar, atualizar e listar templates de email:

\```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Models\EmailTemplate;

class EmailTemplateController extends Controller
{
    public function index()
    {
        $templates = EmailTemplate::all();
        return view('email_templates.index', compact('templates'));
    }

    public function create()
    {
        return view('email_templates.create');
    }

    public function store(Request $request)
    {
        $request->validate([
            'name' => 'required|string',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        EmailTemplate::create($request->all());

        return redirect()->route('email_templates.index')->with('success', 'Template criado com sucesso.');
    }

    public function edit(EmailTemplate $emailTemplate)
    {
        return view('email_templates.edit', compact('emailTemplate'));
    }

    public function update(Request $request, EmailTemplate $emailTemplate)
    {
        $request->validate([
            'name' => 'required|string',
            'subject' => 'required|string',
            'body' => 'required|string',
        ]);

        $emailTemplate->update($request->all());

        return redirect()->route('email_templates.index')->with('success', 'Template atualizado com sucesso.');
    }
}
\```

### Criar Views para Gerenciar Templates

Crie as views `resources/views/email_templates/index.blade.php`, `create.blade.php` e `edit.blade.php` para listar, criar e editar templates de email.

#### Exemplo de View para Listar Templates

\```blade
@extends('layouts.app')

@section('content')
    <div class="container">
        <h1>Templates de Email</h1>
        <a href="{{ route('email_templates.create') }}" class="btn btn-primary">Criar Novo Template</a>
        <table class="table">
            <thead>
                <tr>
                    <th>Nome</th>
                    <th>Assunto</th>
                    <th>Status</th>
                    <th>Ações</th>
                </tr>
            </thead>
            <tbody>
                @foreach($templates as $template)
                    <tr>
                        <td>{{ $template->name }}</td>
                        <td>{{ $template->subject }}</td>
                        <td>{{ $template->status }}</td>
                        <td>
                            <a href="{{ route('email_templates.edit', $template->id) }}" class="btn btn-warning">Editar</a>
                        </td>
                    </tr>
                @endforeach
            </tbody>
        </table>
    </div>
@endsection
\```

## Passo 3: Agendar o Envio de Campanhas

### Definir o Modelo de Campanha

Crie um modelo para representar as campanhas de email:

\```php
php artisan make:model EmailCampaign
\```

### Exemplo de Modelo de Campanha

No arquivo `app/Models/EmailCampaign.php`, defina o modelo:

\```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class EmailCampaign extends Model
{
    use HasFactory;

    protected $fillable = [
        'email_template_id', 'recipient', 'scheduled_at', 'status'
    ];
}
\```

### Criar a Migração

Crie uma migração para a tabela de campanhas de email usando o comando Artisan:

\```bash
php artisan make:migration create_email_campaigns_table
\```

### Exemplo de Migração

No arquivo de migração gerado, defina a estrutura da tabela:

\```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

class CreateEmailCampaignsTable extends Migration
{
    public function up()
    {
        Schema::create('email_campaigns', function (Blueprint $table) {
            $table->id();
            $table->foreignId('email_template_id')->constrained()->onDelete('cascade');
            $table->string('recipient');
            $table->timestamp('scheduled_at');
            $table->string('status')->default('pending'); // pending, sent, failed
            $table->timestamps();
        });
    }

    public function down()
    {
        Schema::dropIfExists('email_campaigns');
    }
}
\```

### Executar a Migração

Execute a migração para criar a tabela:

\```bash
php artisan migrate
\```

## Passo 4: Criar Jobs para Enviar Emails Agendados

### Criar o Job

Use o comando Artisan para criar um job para enviar emails agendados:

\```bash
php artisan make:job SendScheduledEmail
\```

### Exemplo de Job

No arquivo `app/Jobs/SendScheduledEmail.php`, defina a lógica para enviar emails:

\```php
namespace App\Jobs;

use App\Models\EmailCampaign;
use App\Models\EmailTemplate;
use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Mail;

class SendScheduledEmail implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $campaign;

    public function __construct(EmailCampaign $campaign)
    {
        $this->campaign = $campaign;
    }

    public function handle()
    {
        $template = EmailTemplate::find($this->campaign->email_template_id);

        $details = [
            'subject' => $template->subject,
            'body' => $template->body,
        ];

        try {
            Mail::send([], [], function ($message) use ($details) {
                $message->to($this->campaign->recipient)
                        ->subject($details['subject'])
                        ->setBody($details['body'], 'text/html');
            });

            $this->campaign->update(['status' => 'sent']);
        } catch (\Exception $e) {
            $this->campaign->update(['status' => 'failed']);
        }
    }
}
\```

## Passo 5: Agendar o Job com Cron Job

### Agendar o Job no Kernel

No arquivo `app/Console/Kernel.php`, agende o job para ser executado periodicamente:

\```php
protected function schedule(Schedule $schedule)
{
    $schedule->call(function () {
        $campaigns = EmailCampaign::where('status', 'pending')
                                  ->where('scheduled_at', '<=', now())
                                  ->get();

        foreach ($campaigns as $campaign) {
            dispatch(new SendScheduledEmail($campaign));
        }
    })->everyMinute();
}
\```

## Benefícios de Usar Múltiplos Templates de Email

- **Flexibilidade**: Permite criar e gerenciar diversos templates para diferentes campanhas.
- **Automatização**: Facilita o envio automatizado de emails com base em agendamentos.
- **Personalização**: Permite personalizar emails de acordo com a necessidade da campanha e do público-alvo.

## Resumo

Gerenciar múltiplos templates de email no Laravel permite criar, armazenar e enviar campanhas de email automatizadas de forma eficiente. Com modelos para templates de email e campanhas, tabelas no banco de dados para armazenar informações e jobs agendados para enviar emails, você pode personalizar suas campanhas de marketing para diferentes públicos e automatizar o processo de envio. A implementação dessas práticas melhora a flexibilidade, personalização e automação das suas campanhas de email, garantindo que suas mensagens sejam entregues de forma oportuna e eficaz.
