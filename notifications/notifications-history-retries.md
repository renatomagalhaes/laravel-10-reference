# Histórico de Emails Enviados e Retentativas

Manter um histórico de emails enviados e implementar um sistema de retentativas para mensagens falhadas são práticas importantes para garantir a entrega e monitoramento eficazes das notificações. Este guia aborda como configurar esses recursos no Laravel.

## Importância do Histórico de Emails e Retentativas

### Benefícios

- **Rastreabilidade:** Permite rastrear todos os emails enviados e verificar seu status.
- **Diagnóstico:** Facilita a identificação de problemas de entrega.
- **Retentativas Automáticas:** Assegura que emails não entregues sejam reenviados automaticamente.

## Configurando o Histórico de Emails Enviados

### Passo 1: Criar uma Tabela de Histórico

Crie uma tabela para armazenar o histórico de emails enviados. Use o comando Artisan para criar uma migração:

```bash
php artisan make:migration create_email_histories_table --create=email_histories
```

No arquivo de migração gerado, defina a estrutura da tabela:

```php
public function up()
{
    Schema::create('email_histories', function (Blueprint $table) {
        $table->id();
        $table->string('email');
        $table->string('subject');
        $table->text('body');
        $table->string('status');
        $table->timestamps();
    });
}
```

Execute a migração:

```bash
php artisan migrate
```

### Passo 2: Registrar Emails Enviados

No arquivo `app/Notifications/EmailNotification.php`, adicione a lógica para registrar emails enviados no banco de dados:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;
use App\Models\EmailHistory;

class EmailNotification extends Notification
{
    use Queueable;

    private $details;

    public function __construct($details)
    {
        $this->details = $details;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        // Registrar o email no histórico
        EmailHistory::create([
            'email' => $notifiable->email,
            'subject' => 'Email Notification',
            'body' => $this->details['body'],
            'status' => 'sent',
        ]);

        return (new MailMessage)
                    ->subject('Email Notification')
                    ->line($this->details['body'])
                    ->action($this->details['actionText'], $this->details['actionURL'])
                    ->line('Thank you for using our application!');
    }
}
```

### Passo 3: Verificar o Histórico de Emails

Crie um modelo e controlador para visualizar o histórico de emails enviados:

#### Modelo

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class EmailHistory extends Model
{
    use HasFactory;

    protected $fillable = ['email', 'subject', 'body', 'status'];
}
```

#### Controlador

```php
namespace App\Http\Controllers;

use App\Models\EmailHistory;
use Illuminate\Http\Request;

class EmailHistoryController extends Controller
{
    public function index()
    {
        $histories = EmailHistory::all();
        return view('email_histories.index', compact('histories'));
    }
}
```

#### Rota

```php
Route::get('/email-histories', 'EmailHistoryController@index');
```

## Implementando Retentativas Automáticas

### Passo 1: Configurar Retentativas no Laravel

No arquivo `config/queue.php`, configure o número de tentativas:

```php
'connections' => [
    'redis' => [
        'driver' => 'redis',
        'connection' => 'default',
        'queue' => env('REDIS_QUEUE', 'default'),
        'retry_after' => 90,
        'block_for' => null,
    ],
],
```

### Passo 2: Definir Retentativas na Notificação

No arquivo `app/Notifications/EmailNotification.php`, configure as tentativas e o tempo de espera entre elas:

```php
public function via($notifiable)
{
    return ['mail'];
}

public function failed($exception)
{
    // Atualizar o status para 'failed'
    EmailHistory::create([
        'email' => $notifiable->email,
        'subject' => 'Email Notification',
        'body' => $this->details['body'],
        'status' => 'failed',
    ]);
}

public function toMail($notifiable)
{
    return (new MailMessage)
                ->subject('Email Notification')
                ->line($this->details['body'])
                ->action($this->details['actionText'], $this->details['actionURL'])
                ->line('Thank you for using our application!')
                ->onConnection('redis')
                ->onQueue('emails')
                ->delay(now()->addMinutes(5));
}
```

### Passo 3: Processar Retentativas

Certifique-se de que o worker de filas esteja configurado para processar retentativas:

```bash
php artisan queue:work --tries=3
```

## Exemplo Prático

### Backend

1. Crie a tabela de histórico de emails.
2. Registre emails enviados e falhas no histórico.
3. Configure retentativas automáticas para emails falhados.

### Frontend

Crie uma interface administrativa para visualizar o histórico de emails enviados e gerenciar as retentativas.

## Resumo

Manter um histórico de emails enviados e implementar retentativas automáticas são práticas essenciais para garantir a entrega eficaz e monitoramento das suas notificações. Com a configuração adequada, você pode melhorar significativamente a rastreabilidade e a confiabilidade do seu sistema de notificações.
