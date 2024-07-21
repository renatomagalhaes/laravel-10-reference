# Validação de Endereços de Email

Validar endereços de email antes de enviar mensagens é uma prática essencial para garantir a qualidade das suas listas de contatos, melhorar a entregabilidade e evitar problemas com provedores de email.

## Passo 1: Validação Básica no Laravel

### Validação com Regras de Validação

O Laravel oferece validação básica de endereços de email usando regras de validação. No seu controlador, você pode validar os endereços de email da seguinte forma:

```php
use Illuminate\Http\Request;

class EmailController extends Controller
{
    public function send(Request $request)
    {
        $request->validate([
            'email' => 'required|email',
        ]);

        // Lógica para envio de email
    }
}
```

## Passo 2: Validação de Sintaxe e DNS

### Validação de DNS

Além da validação de sintaxe, é importante verificar se o domínio do email realmente existe. Você pode fazer isso usando a função `checkdnsrr` do PHP.

```php
public function validateEmailDomain($email)
{
    $domain = substr(strrchr($email, "@"), 1);

    return checkdnsrr($domain, 'MX');
}
```

No seu controlador, você pode usar essa função para validar o domínio:

```php
public function send(Request $request)
{
    $request->validate([
        'email' => 'required|email',
    ]);

    if (!$this->validateEmailDomain($request->email)) {
        return response()->json(['message' => 'O domínio do email não é válido'], 422);
    }

    // Lógica para envio de email
}
```

## Passo 3: Validação de Email em Tempo Real

### Usar Serviços de Validação de Email

Existem serviços externos que fornecem validação de email em tempo real, como Mailgun, SendGrid e EmailListVerify. Esses serviços podem verificar se o email é válido e se está ativo.

#### Exemplo de Uso do Mailgun

1. Acesse o painel de controle do Mailgun e obtenha a chave da API.
2. Instale o pacote GuzzleHTTP para fazer requisições HTTP:

```bash
composer require guzzlehttp/guzzle
```

3. No seu controlador, faça a requisição de validação:

```php
use GuzzleHttp\Client;

public function validateEmailWithMailgun($email)
{
    $client = new Client();
    $response = $client->request('GET', 'https://api.mailgun.net/v4/address/validate', [
        'auth' => ['api', 'your_mailgun_api_key'],
        'query' => ['address' => $email],
    ]);

    return json_decode($response->getBody(), true);
}

public function send(Request $request)
{
    $request->validate([
        'email' => 'required|email',
    ]);

    $validation = $this->validateEmailWithMailgun($request->email);

    if (!$validation['is_valid']) {
        return response()->json(['message' => 'O email não é válido'], 422);
    }

    // Lógica para envio de email
}
```

## Passo 4: Validação de Email em Lote

### Validar Listas de Emails

Se você precisar validar uma lista grande de emails, pode usar o mesmo serviço de validação em tempo real, mas processar os emails em lote.

#### Exemplo de Validação em Lote

```php
public function validateEmailBatch(array $emails)
{
    $client = new Client();
    $responses = [];

    foreach ($emails as $email) {
        $response = $client->request('GET', 'https://api.mailgun.net/v4/address/validate', [
            'auth' => ['api', 'your_mailgun_api_key'],
            'query' => ['address' => $email],
        ]);
        $responses[$email] = json_decode($response->getBody(), true);
    }

    return $responses;
}

public function sendBatch(Request $request)
{
    $emails = $request->input('emails');
    $validEmails = [];
    $invalidEmails = [];

    foreach ($this->validateEmailBatch($emails) as $email => $result) {
        if ($result['is_valid']) {
            $validEmails[] = $email;
        } else {
            $invalidEmails[] = $email;
        }
    }

    // Lógica para envio de email para $validEmails
}
```

## Benefícios da Validação de Endereços de Email

- **Melhoria da Entregabilidade**: Reduz a chance de bounces e aumenta a taxa de entrega.
- **Proteção da Reputação do Domínio**: Minimiza o risco de ser marcado como spam.
- **Qualidade da Lista de Contatos**: Garante que apenas emails válidos e ativos sejam adicionados à sua lista.

## Resumo

A validação de endereços de email é um processo crítico para manter a qualidade das suas listas de contatos e garantir a entregabilidade das suas campanhas de email. Utilizando validação básica, verificação de DNS e serviços externos de validação, você pode melhorar significativamente a eficácia das suas comunicações por email.
