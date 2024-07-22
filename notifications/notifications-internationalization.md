# Internacionalização de Notificações

A internacionalização (i18n) de notificações no Laravel permite que você envie mensagens traduzidas para diferentes idiomas, proporcionando uma experiência mais inclusiva e personalizada para usuários globais. Este guia aborda como configurar e implementar a internacionalização para suas notificações.

## Configuração Inicial para Internacionalização

### Passo 1: Definir Arquivos de Tradução

Os arquivos de tradução são armazenados no diretório `resources/lang`. Cada idioma tem seu próprio subdiretório. Por exemplo, para inglês e português, você teria `resources/lang/en` e `resources/lang/pt`.

Dentro desses diretórios, crie arquivos PHP que retornem arrays de strings traduzidas. Por exemplo, em `resources/lang/en/notifications.php`:

```php
return [
    'greeting' => 'Hello!',
    'body' => 'This is your notification message.',
    'actionText' => 'View Details',
    'thanks' => 'Thank you for using our application!',
];
```

E em `resources/lang/pt/notifications.php`:

```php
return [
    'greeting' => 'Olá!',
    'body' => 'Esta é sua mensagem de notificação.',
    'actionText' => 'Ver Detalhes',
    'thanks' => 'Obrigado por usar nossa aplicação!',
];
```

### Passo 2: Utilizar Traduções na Notificação

Configure sua notificação para utilizar as traduções definidas nos arquivos de linguagem. No arquivo `app/Notifications/InternationalizedNotification.php`:

```php
namespace App\Notifications;

use Illuminate\Bus\Queueable;
use Illuminate\Notifications\Messages\MailMessage;
use Illuminate\Notifications\Notification;
use Illuminate\Support\Facades\App;

class InternationalizedNotification extends Notification
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
        $locale = $notifiable->preferred_locale ?? App::getLocale();
        App::setLocale($locale);

        return (new MailMessage)
                    ->subject(__('notifications.greeting'))
                    ->line(__('notifications.body'))
                    ->action(__('notifications.actionText'), $this->details['actionURL'])
                    ->line(__('notifications.thanks'));
    }
}
```

### Passo 3: Definir a Preferência de Idioma do Usuário

Adicione um campo ao modelo do usuário para armazenar a preferência de idioma. Por exemplo:

```php
Schema::table('users', function (Blueprint $table) {
    $table->string('preferred_locale')->default('en');
});
```

### Passo 4: Atualizar Preferência de Idioma do Usuário

Permita que os usuários atualizem sua preferência de idioma através de um formulário na aplicação:

```php
public function updateLocale(Request $request)
{
    $user = Auth::user();
    $user->preferred_locale = $request->input('preferred_locale');
    $user->save();

    return back()->with('status', 'Language preference updated!');
}
```

### Passo 5: Enviar a Notificação Internacionalizada

Para enviar a notificação, use o método `notify` no modelo do usuário:

```php
use App\Notifications\InternationalizedNotification;

$user = User::find(1);
$details = [
    'actionURL' => url('/'),
];

$user->notify(new InternationalizedNotification($details));
```

## Exemplo Prático

### Backend

Configure arquivos de tradução para cada idioma suportado e ajuste o modelo de usuário para incluir a preferência de idioma.

### Frontend

Adicione um formulário para permitir que os usuários atualizem sua preferência de idioma:

```html
<form action="{{ route('updateLocale') }}" method="POST">
    @csrf
    <label for="preferred_locale">Preferred Language:</label>
    <select name="preferred_locale" id="preferred_locale">
        <option value="en" {{ auth()->user()->preferred_locale == 'en' ? 'selected' : '' }}>English</option>
        <option value="pt" {{ auth()->user()->preferred_locale == 'pt' ? 'selected' : '' }}>Português</option>
    </select>
    <button type="submit">Update Language</button>
</form>
```

## Resumo

A internacionalização de notificações no Laravel permite que você envie mensagens personalizadas e traduzidas para diferentes idiomas, melhorando a experiência do usuário globalmente. Com uma configuração adequada dos arquivos de tradução e ajuste das preferências de idioma dos usuários, você pode garantir que suas notificações sejam compreendidas por todos os seus usuários, independentemente do idioma que falem.
