# Validação de Endereços de Email

A validação de endereços de email é uma prática importante para garantir que as notificações sejam enviadas para endereços válidos e entregues corretamente. A validação pode ser feita em diferentes níveis, desde a validação sintática básica até a validação avançada usando serviços externos.

## Importância da Validação de Endereços de Email

### Benefícios

- **Redução de Bounces:** Minimiza a taxa de emails devolvidos por endereços inválidos.
- **Melhoria na Entregabilidade:** Aumenta a chance de os emails serem entregues na caixa de entrada.
- **Economia de Recursos:** Evita o envio de emails para endereços inválidos, economizando recursos.

## Validação Básica no Laravel

### Passo 1: Validação Sintática

Use a validação embutida do Laravel para verificar se o formato do email é válido:

```php
$request->validate([
    'email' => 'required|email',
]);
```

Essa regra verifica se o email possui um formato válido (por exemplo, `user@example.com`).

### Passo 2: Verificação de Existência de DNS

Além da validação sintática, você pode verificar se o domínio do email possui registros DNS válidos. Para isso, você pode usar a classe `EmailValidator` do pacote `egulias/email-validator`:

```bash
composer require egulias/email-validator
```

No seu validador personalizado:

```php
use Egulias\EmailValidator\EmailValidator;
use Egulias\EmailValidator\Validation\DNSCheckValidation;

public function validateEmail($email)
{
    $validator = new EmailValidator();
    return $validator->isValid($email, new DNSCheckValidation());
}
```

### Passo 3: Validação de SMTP

Para uma validação ainda mais robusta, você pode verificar se o servidor SMTP do domínio aceita emails para o endereço especificado. Isso pode ser feito usando pacotes como `sokil/php-isemail`:

```bash
composer require sokil/php-isemail
```

Exemplo de uso:

```php
use Sokil\IsEmail\IsEmail;

public function validateEmail($email)
{
    $validator = new IsEmail();
    return $validator->validate($email) === IsEmail::VALID;
}
```

## Validação Avançada com Serviços Externos

### Passo 1: Uso de Serviços de Validação

Serviços de validação de emails, como Kickbox, ZeroBounce, ou Hunter, oferecem APIs que verificam a validade dos endereços de email, incluindo verificações de SMTP e de existência.

### Passo 2: Configuração de um Serviço de Validação

Como exemplo, vamos usar o Kickbox. Primeiro, registre-se no Kickbox e obtenha sua chave API. Em seguida, configure o Kickbox no Laravel.

#### Configuração no Arquivo `.env`

```env
KICKBOX_API_KEY=your_kickbox_api_key
```

#### Criação de um Serviço de Validação

No seu serviço de validação:

```php
use GuzzleHttp\Client;

public function validateEmail($email)
{
    $client = new Client();
    $response = $client->request('GET', 'https://api.kickbox.com/v2/verify', [
        'query' => [
            'email' => $email,
            'apikey' => env('KICKBOX_API_KEY')
        ]
    ]);

    $data = json_decode($response->getBody(), true);
    return $data['result'] === 'deliverable';
}
```

### Passo 3: Integrar a Validação no Processo de Notificação

Antes de enviar uma notificação, valide o endereço de email:

```php
$email = $request->input('email');

if ($this->validateEmail($email)) {
    $user->notify(new EmailNotification($details));
} else {
    // Trate o caso de email inválido
}
```

## Exemplo Prático

### Backend

1. Implemente validações sintáticas e de DNS.
2. Configure e integre serviços externos de validação de email.
3. Adicione lógica para tratar emails inválidos antes de enviar notificações.

### Frontend

No frontend, você pode implementar validações em tempo real usando JavaScript ou bibliotecas como `validator.js` para fornecer feedback imediato ao usuário sobre a validade do email.

```html
<form id="email-form">
    <input type="email" id="email" name="email" required>
    <button type="submit">Submit</button>
</form>
<script src="https://cdn.jsdelivr.net/npm/validator@13.7.0/validator.min.js"></script>
<script>
    document.getElementById('email-form').addEventListener('submit', function(event) {
        var email = document.getElementById('email').value;
        if (!validator.isEmail(email)) {
            alert('Email inválido');
            event.preventDefault();
        }
    });
</script>
```

## Resumo

A validação de endereços de email é uma prática crucial para garantir a entrega bem-sucedida de notificações. Combinando validações básicas e avançadas, incluindo serviços externos, você pode melhorar significativamente a qualidade e a eficácia das suas campanhas de notificação.
