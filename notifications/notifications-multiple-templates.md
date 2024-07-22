# Trabalhando com Múltiplos Templates de Email para Campanhas Automatizadas

Usar múltiplos templates de email é uma prática eficiente para gerenciar campanhas automatizadas. Isso permite personalizar e otimizar o conteúdo dos emails de acordo com diferentes segmentos da audiência, ocasiões ou campanhas específicas.

## Importância de Múltiplos Templates de Email

### Benefícios

- **Personalização:** Cria mensagens mais relevantes e personalizadas para diferentes segmentos de audiência.
- **Flexibilidade:** Facilita a adaptação de emails para diferentes campanhas sem a necessidade de recriar o conteúdo do zero.
- **Otimização:** Permite testar diferentes layouts e conteúdos para melhorar a eficácia das campanhas.

## Configurando Múltiplos Templates de Email no Laravel

### Passo 1: Criar Templates de Email

Crie templates de email no diretório `resources/views/emails`. Por exemplo, crie templates para uma campanha de boas-vindas e uma campanha de promoção:

#### Template de Boas-Vindas

```blade
<!-- resources/views/emails/welcome.blade.php -->
@component('mail::message')
# Bem-vindo, {{ $user->name }}

Estamos felizes em tê-lo conosco. Aqui estão alguns recursos para começar:

@component('mail::button', ['url' => $url])
Comece Agora
@endcomponent

Obrigado,<br>
{{ config('app.name') }}
@endcomponent
```

#### Template de Promoção

```blade
<!-- resources/views/emails/promotion.blade.php -->
@component('mail::message')
# Oferta Especial para Você, {{ $user->name }}

Aproveite nossa promoção exclusiva com 20% de desconto em todos os produtos.

@component('mail::button', ['url' => $url])
Aproveite a Oferta
@endcomponent

Obrigado,<br>
{{ config('app.name') }}
@endcomponent
```

### Passo 2: Configurar Notificações para Usar Templates

Crie notificações que utilizam esses templates para diferentes campanhas:

#### Notificação de Boas-Vindas

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class WelcomeNotification extends Notification
{
    use Queueable;

    private $user;
    private $url;

    public function __construct($user, $url)
    {
        $this->user = $user;
        $this->url = $url;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Bem-vindo ao ' . config('app.name'))
                    ->view('emails.welcome', ['user' => $this->user, 'url' => $this->url]);
    }
}
```

#### Notificação de Promoção

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class PromotionNotification extends Notification
{
    use Queueable;

    private $user;
    private $url;

    public function __construct($user, $url)
    {
        $this->user = $user;
        $this->url = $url;
    }

    public function via($notifiable)
    {
        return ['mail'];
    }

    public function toMail($notifiable)
    {
        return (new MailMessage)
                    ->subject('Oferta Especial do ' . config('app.name'))
                    ->view('emails.promotion', ['user' => $this->user, 'url' => $this->url]);
    }
}
```

### Passo 3: Programar Envios Automáticos

Use um agendador de tarefas (Scheduler) para programar o envio de emails. No arquivo `app/Console/Kernel.php`, configure as tarefas agendadas:

```php
protected function schedule(Schedule $schedule)
{
    $schedule->call(function () {
        $users = User::where('welcome_email_sent', false)->get();
        foreach ($users as $user) {
            $user->notify(new WelcomeNotification($user, url('/welcome')));
            $user->update(['welcome_email_sent' => true]);
        }
    })->daily();

    $schedule->call(function () {
        $users = User::where('promotion_email_sent', false)->get();
        foreach ($users as $user) {
            $user->notify(new PromotionNotification($user, url('/promotion')));
            $user->update(['promotion_email_sent' => true]);
        }
    })->weekly();
}
```

### Passo 4: Gerenciar Templates no Banco de Dados

Para uma abordagem mais dinâmica, armazene templates de email no banco de dados e carregue-os conforme necessário.

#### Migração

```php
php artisan make:migration create_email_templates_table --create=email_templates
```

No arquivo de migração:

```php
public function up()
{
    Schema::create('email_templates', function (Blueprint $table) {
        $table->id();
        $table->string('name');
        $table->text('subject');
        $table->longText('body');
        $table->timestamps();
    });
}
```

Execute a migração:

```bash
php artisan migrate
```

#### Modelo

```php
namespace App\Models;

use Illuminate\Database\Eloquent\Factories\HasFactory;
use Illuminate\Database\Eloquent\Model;

class EmailTemplate extends Model
{
    use HasFactory;

    protected $fillable = ['name', 'subject', 'body'];
}
```

#### Uso de Templates Dinâmicos

Carregue e use templates do banco de dados:

```php
use App\Models\EmailTemplate;

$template = EmailTemplate::where('name', 'welcome')->first();
$mailMessage = (new MailMessage)
                ->subject($template->subject)
                ->view(['html' => $template->body], ['user' => $user, 'url' => $url]);

$user->notify(new CustomEmailNotification($mailMessage));
```

## Resumo

Usar múltiplos templates de email no Laravel para campanhas automatizadas permite maior flexibilidade e personalização nas suas comunicações. Com a configuração adequada e o uso de agendadores, você pode gerenciar eficazmente suas campanhas e melhorar a experiência do usuário.
