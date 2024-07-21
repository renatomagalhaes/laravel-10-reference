# Monitoramento de Bounce e Feedback Loop

Monitorar bounces (falhas de entrega) e configurar feedback loops são práticas essenciais para manter a reputação do seu domínio de email e melhorar a entregabilidade das suas campanhas de email.

## Passo 1: Configurar Webhooks para Bounces

### Usar Provedor de Email

Se você estiver usando um provedor de email como Mailgun, SendGrid ou Amazon SES, configure webhooks para receber notificações sobre bounces e feedback loops.

#### Exemplo de Configuração no Mailgun

1. Acesse o painel de controle do Mailgun.
2. Vá para a seção "Webhooks".
3. Adicione um novo webhook para bounces, configurando a URL para onde os eventos de bounce serão enviados.

### Criar Rota para Receber Webhooks

No Laravel, crie uma rota para receber e processar os eventos de bounce.

```php
use Illuminate\Http\Request;

Route::post('/webhook/mailgun', function (Request $request) {
    // Processar o evento recebido
    $event = $request->input('event-data');

    if ($event['event'] === 'failed' && $event['severity'] === 'permanent') {
        // Lógica para lidar com o bounce
    }

    return response()->json(['status' => 'success']);
});
```

## Passo 2: Processar Bounces e Atualizar a Base de Dados

### Exemplo de Código para Processar Bounces

No controlador, adicione a lógica para atualizar a base de dados com os emails que falharam:

```php
use App\Models\EmailBounce;
use Illuminate\Http\Request;

class WebhookController extends Controller
{
    public function handleMailgunWebhook(Request $request)
    {
        $event = $request->input('event-data');

        if ($event['event'] === 'failed' && $event['severity'] === 'permanent') {
            // Salvar informações do bounce na base de dados
            EmailBounce::create([
                'email' => $event['recipient'],
                'reason' => $event['reason'],
                'event' => $event['event'],
                'timestamp' => $event['timestamp'],
            ]);
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Passo 3: Configurar Feedback Loops

### Configurar Feedback Loop no Provedor de Email

Muitos provedores de email suportam a configuração de feedback loops (FBL), que permitem receber notificações quando um email é marcado como spam.

#### Exemplo de Configuração no Amazon SES

1. Acesse o painel de controle do Amazon SES.
2. Vá para a seção "Email Feedback".
3. Configure o feedback loop para enviar notificações para um tópico SNS (Simple Notification Service).

### Criar Rota para Receber Notificações de Feedback

No Laravel, crie uma rota para receber e processar os eventos de feedback loop.

```php
use Illuminate\Http\Request;

Route::post('/webhook/ses', function (Request $request) {
    // Processar o evento recebido
    $notification = json_decode($request->getContent(), true);

    if (isset($notification['complaint'])) {
        // Lógica para lidar com a reclamação
    }

    return response()->json(['status' => 'success']);
});
```

## Passo 4: Atualizar a Base de Dados com Feedbacks

### Exemplo de Código para Processar Feedbacks

No controlador, adicione a lógica para atualizar a base de dados com os emails que receberam reclamações:

```php
use App\Models\EmailFeedback;
use Illuminate\Http\Request;

class WebhookController extends Controller
{
    public function handleSESWebhook(Request $request)
    {
        $notification = json_decode($request->getContent(), true);

        if (isset($notification['complaint'])) {
            // Salvar informações do feedback na base de dados
            EmailFeedback::create([
                'email' => $notification['complaint']['complainedRecipients'][0]['emailAddress'],
                'feedbackType' => $notification['complaint']['complaintFeedbackType'],
                'timestamp' => $notification['complaint']['timestamp'],
            ]);
        }

        return response()->json(['status' => 'success']);
    }
}
```

## Benefícios do Monitoramento de Bounce e Feedback Loop

- **Melhoria da Entregabilidade**: Ajuda a identificar e remover emails inválidos, melhorando a taxa de entrega.
- **Proteção da Reputação do Domínio**: Minimiza o risco de ser marcado como spam, protegendo a reputação do seu domínio.
- **Diagnóstico de Problemas**: Fornece informações valiosas para diagnosticar e resolver problemas de entrega de emails.

## Resumo

Monitorar bounces e configurar feedback loops são práticas essenciais para manter a saúde da sua lista de emails e proteger a reputação do seu domínio. Com a configuração correta de webhooks e a lógica de processamento no Laravel, você pode garantir que suas campanhas de email sejam entregues de forma eficiente e segura.
