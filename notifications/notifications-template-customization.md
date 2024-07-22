# Personalização de Templates de Notificação

Personalizar templates de notificação no Laravel permite que você crie emails com um design consistente que corresponde à identidade visual da sua aplicação. Com a flexibilidade dos componentes Blade e Markdown, você pode facilmente ajustar os templates para atender às suas necessidades.

## Configurando Templates de Email

### Passo 1: Criar um Template Personalizado

Para começar, crie um novo template Blade para seus emails. Por exemplo, crie um arquivo chamado `custom.blade.php` em `resources/views/vendor/mail/html`.

```php
<!DOCTYPE html>
<html>
<head>
    <meta charset="utf-8">
    <style>
        /* Adicione seu CSS personalizado aqui */
        body {
            font-family: Arial, sans-serif;
            background-color: #f8f9fa;
            color: #333;
        }
        .header, .footer {
            background-color: #343a40;
            color: white;
            padding: 10px;
            text-align: center;
        }
        .content {
            padding: 20px;
            background-color: white;
        }
        .button {
            display: inline-block;
            padding: 10px 20px;
            margin: 20px 0;
            background-color: #007bff;
            color: white;
            text-decoration: none;
            border-radius: 5px;
        }
    </style>
</head>
<body>
    <div class="header">
        <h1>{{ config('app.name') }}</h1>
    </div>
    <div class="content">
        @yield('content')
    </div>
    <div class="footer">
        © {{ date('Y') }} {{ config('app.name') }}. All rights reserved.
    </div>
</body>
</html>
```

### Passo 2: Utilizar o Template em uma Notificação

Configure sua notificação para usar o template personalizado:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;

class CustomTemplateNotification extends Notification
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
                    ->subject('Custom Template Notification')
                    ->view('vendor.mail.html.custom', ['details' => $this->details]);
    }
}
```

### Passo 3: Enviar a Notificação

Envie a notificação usando o método `notify` no modelo do usuário:

```php
use App\Notifications\CustomTemplateNotification;

$user = User::find(1);
$details = [
    'body' => 'This is a custom template notification',
    'actionText' => 'View Details',
    'actionURL' => url('/'),
];

$user->notify(new CustomTemplateNotification($details));
```

## Utilização de Componentes Blade em Notificações

O Laravel permite o uso de componentes Blade para tornar seus templates mais modulares e reutilizáveis. Por exemplo, crie um componente de botão reutilizável.

### Passo 1: Criar o Componente Blade

Crie um componente de botão em `resources/views/components/button.blade.php`:

```php
<a href="{{ $url }}" class="button">{{ $slot }}</a>
```

### Passo 2: Utilizar o Componente no Template

No seu template personalizado, utilize o componente de botão:

```php
@extends('vendor.mail.html.custom')

@section('content')
    <p>{{ $details['body'] }}</p>
    @component('components.button', ['url' => $details['actionURL']])
        {{ $details['actionText'] }}
    @endcomponent
@endsection
```

## Resumo

Personalizar templates de notificação no Laravel permite que você mantenha um design consistente e alinhado com a identidade visual da sua aplicação. Utilizando componentes Blade e templates personalizados, você pode criar notificações atraentes e funcionais que melhoram a experiência do usuário.
