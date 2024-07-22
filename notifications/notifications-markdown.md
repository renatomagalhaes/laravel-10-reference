# Uso de Markdown para Notificações

O Laravel permite o uso de Markdown para formatar notificações de email de maneira simples e elegante. O uso de Markdown facilita a criação de emails que são tanto visualmente atraentes quanto fáceis de manter.

## Benefícios do Uso de Markdown

- **Simplicidade:** Markdown é uma linguagem de marcação simples e fácil de usar.
- **Estilo Consistente:** Facilita a aplicação de estilos consistentes em todas as notificações de email.
- **Manutenção Fácil:** Torna o código mais legível e fácil de manter.

## Criando Notificações com Markdown

### Passo 1: Criar uma Notificação com Markdown

Use o comando Artisan para criar uma notificação com Markdown:

```bash
php artisan make:notification MarkdownNotification --markdown=emails.notification
```

Isso criará uma nova notificação em `app/Notifications/MarkdownNotification.php` e um template Markdown em `resources/views/emails/notification.blade.php`.

### Passo 2: Configurar a Notificação

No arquivo `app/Notifications/MarkdownNotification.php`, configure a notificação para usar o template Markdown:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class MarkdownNotification extends Notification
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
        return (new MailMessage)
                    ->subject('Markdown Notification')
                    ->markdown('emails.notification', ['details' => $this->details]);
    }
}
```

### Passo 3: Criar o Template Markdown

No arquivo `resources/views/emails/notification.blade.php`, defina o conteúdo da notificação usando Markdown:

```markdown
@component('mail::message')
# Introduction

{{ $details['body'] }}

@component('mail::button', ['url' => $details['actionURL']])
{{ $details['actionText'] }}
@endcomponent

Thanks,<br>
{{ config('app.name') }}
@endcomponent
```

### Passo 4: Enviar a Notificação

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\MarkdownNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a test Markdown notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new MarkdownNotification($details));
```

## Personalizando o Template Markdown

Você pode personalizar ainda mais seus templates Markdown, aproveitando as diretivas do Blade e os componentes de email do Laravel.

### Adicionar Componentes Personalizados

Você pode criar componentes personalizados e reutilizáveis para seus templates Markdown. Por exemplo, crie um componente para um rodapé de email:

```php
// resources/views/vendor/mail/html/footer.blade.php
@component('mail::footer')
© {{ date('Y') }} {{ config('app.name') }}. All rights reserved.
@endcomponent
```

E use-o em seu template Markdown:

```markdown
@component('mail::message')
# Introduction

{{ $details['body'] }}

@component('mail::button', ['url' => $details['actionURL']])
{{ $details['actionText'] }}
@endcomponent

@component('mail::footer')
@endcomponent
@endcomponent
```

## Resumo

O uso de Markdown para notificações no Laravel facilita a criação de emails formatados de maneira consistente e elegante. Com a capacidade de personalizar templates e adicionar componentes reutilizáveis, você pode manter um estilo consistente em todas as suas notificações de email.
