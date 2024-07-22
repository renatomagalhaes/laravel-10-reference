# Monitoramento de Bounce e Feedback Loop

O monitoramento de bounces e a configuração de feedback loops são essenciais para garantir a entregabilidade dos seus emails e manter a reputação do seu domínio. Este guia aborda como configurar e monitorar bounces e feedback loops no Laravel.

## O que são Bounces e Feedback Loops?

### Bounces

- **Soft Bounce:** Um problema temporário impede a entrega do email, como uma caixa de entrada cheia.
- **Hard Bounce:** Um problema permanente impede a entrega do email, como um endereço de email inválido.

### Feedback Loops

Feedback loops permitem que provedores de email reportem emails marcados como spam pelos destinatários, ajudando a manter a reputação do seu domínio.

## Configurando o Monitoramento de Bounces

### Passo 1: Escolher um Serviço de Email com Suporte a Bounces

Serviços como AWS SES, SendGrid e Mailgun oferecem suporte a bounces e podem ser configurados para enviar notificações de bounces.

### Passo 2: Configurar Bounces no AWS SES

#### Configuração no Painel do SES

1. No painel do SES, vá para **Email Addresses** ou **Domains**.
2. Selecione o endereço de email ou domínio que deseja configurar.
3. Configure uma SNS Topic para bounces e configure o SES para enviar notificações para essa SNS Topic.

#### Configuração de uma SNS Topic

1. No painel do SNS, crie uma nova SNS Topic.
2. Configure a SNS Topic para enviar notificações para um endpoint HTTP/S, email ou outra integração.

### Passo 3: Criar um Endpoint HTTP para Receber Notificações de Bounces

Crie uma rota e um controlador no Laravel para processar notificações de bounces.

#### Rota

```php
Route::post('/webhook/ses-bounce', 'SESController@handleBounce');
```

#### Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class SESController extends Controller
{
    public function handleBounce(Request $request)
    {
        $message = json_decode($request->getContent(), true);

        if (isset($message['Type']) && $message['Type'] == 'Notification') {
            $notification = json_decode($message['Message'], true);

            if ($notification['notificationType'] == 'Bounce') {
                foreach ($notification['bounce']['bouncedRecipients'] as $recipient) {
                    $email = $recipient['emailAddress'];
                    Log::info("Bounce detected for email: $email");

                    // Processar o email com bounce
                    // ...
                }
            }
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Configurando Feedback Loops

### Passo 1: Configurar Feedback Loops no Serviço de Email

Serviços como AWS SES, SendGrid e Mailgun suportam feedback loops e podem ser configurados para enviar notificações de spam.

#### Configuração no AWS SES

1. No painel do SES, vá para **Email Addresses** ou **Domains**.
2. Selecione o endereço de email ou domínio que deseja configurar.
3. Configure uma SNS Topic para feedback loops e configure o SES para enviar notificações para essa SNS Topic.

### Passo 2: Criar um Endpoint HTTP para Receber Notificações de Feedback

Crie uma rota e um controlador no Laravel para processar notificações de feedback.

#### Rota

```php
Route::post('/webhook/ses-feedback', 'SESController@handleFeedback');
```

#### Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Log;

class SESController extends Controller
{
    public function handleFeedback(Request $request)
    {
        $message = json_decode($request->getContent(), true);

        if (isset($message['Type']) && $message['Type'] == 'Notification') {
            $notification = json_decode($message['Message'], true);

            if ($notification['notificationType'] == 'Complaint') {
                foreach ($notification['complaint']['complainedRecipients'] as $recipient) {
                    $email = $recipient['emailAddress'];
                    Log::info("Complaint detected for email: $email");

                    // Processar o email com reclamação
                    // ...
                }
            }
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Exemplo Prático

### Backend

1. Configure bounces e feedback loops no serviço de email.
2. Crie endpoints HTTP no Laravel para processar notificações de bounces e feedback.

### Frontend

Não há configurações específicas no frontend para o monitoramento de bounces e feedback loops, mas você pode criar interfaces administrativas para visualizar os logs e as estatísticas de entrega.

## Resumo

O monitoramento de bounces e a configuração de feedback loops são essenciais para manter a reputação do seu domínio e garantir a entregabilidade dos seus emails. Com a configuração adequada e o processamento automatizado de notificações, você pode gerenciar bounces e reclamações de maneira eficiente.
