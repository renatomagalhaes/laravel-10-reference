# Implementação de SPF, DKIM e DMARC

SPF, DKIM e DMARC são mecanismos de autenticação de email que ajudam a proteger seu domínio contra spoofing e phishing. Implementar essas tecnologias garante que os emails enviados sejam legítimos e aumentam as chances de entrega na caixa de entrada dos destinatários.

## O que são SPF, DKIM e DMARC?

### SPF (Sender Policy Framework)

SPF permite que o proprietário de um domínio especifique quais servidores de email estão autorizados a enviar emails em nome desse domínio. Isso ajuda a evitar que spammers enviem emails falsificados do seu domínio.

### DKIM (DomainKeys Identified Mail)

DKIM adiciona uma assinatura digital aos emails, permitindo que o destinatário verifique se o email foi realmente enviado e autorizado pelo domínio do remetente. Isso garante a integridade do conteúdo do email.

### DMARC (Domain-based Message Authentication, Reporting, and Conformance)

DMARC usa SPF e DKIM para autenticar emails e especifica uma política para lidar com emails que falham na autenticação. Ele também fornece relatórios sobre a atividade de email do seu domínio.

## Configuração de SPF

### Passo 1: Criar um Registro SPF

Adicione um registro TXT ao seu DNS com a política SPF. Um exemplo de registro SPF:

```plaintext
v=spf1 include:spf.example.com ~all
```

- `v=spf1`: Indica a versão do SPF.
- `include:spf.example.com`: Autoriza servidores listados em `spf.example.com` a enviar emails.
- `~all`: Define a política (soft fail) para emails que não correspondem à política SPF.

### Passo 2: Publicar o Registro SPF

No painel de controle do seu provedor de DNS, adicione o registro TXT com o conteúdo acima.

## Configuração de DKIM

### Passo 1: Gerar Chaves DKIM

Use um gerador de chaves DKIM para criar uma chave pública e uma chave privada. Você pode usar ferramentas como `opendkim-genkey`.

```bash
opendkim-genkey -t -s default -d yourdomain.com
```

### Passo 2: Publicar a Chave Pública DKIM

Adicione um registro TXT ao seu DNS para a chave pública DKIM. O nome do registro deve seguir o formato `selector._domainkey.yourdomain.com`.

```plaintext
default._domainkey IN TXT "v=DKIM1; k=rsa; p=YOUR_PUBLIC_KEY"
```

### Passo 3: Configurar o Servidor de Email

Configure seu servidor de email para assinar os emails com a chave privada DKIM. Por exemplo, no Postfix:

```plaintext
smtpd_milters = inet:localhost:12345
non_smtpd_milters = $smtpd_milters
milter_default_action = accept
milter_protocol = 2
```

## Configuração de DMARC

### Passo 1: Criar um Registro DMARC

Adicione um registro TXT ao seu DNS com a política DMARC. Um exemplo de registro DMARC:

```plaintext
_dmarc.yourdomain.com IN TXT "v=DMARC1; p=none; rua=mailto:dmarc-reports@yourdomain.com"
```

- `v=DMARC1`: Indica a versão do DMARC.
- `p=none`: Política de monitoramento, você pode alterar para `quarantine` ou `reject`.
- `rua=mailto:dmarc-reports@yourdomain.com`: Endereço de email para receber relatórios DMARC.

### Passo 2: Publicar o Registro DMARC

No painel de controle do seu provedor de DNS, adicione o registro TXT com o conteúdo acima.

## Teste e Verificação

### Verificar SPF

Use ferramentas como `Kitterman SPF Validator` para verificar o registro SPF.

### Verificar DKIM

Envie um email para `check-auth@verifier.port25.com` e verifique o relatório recebido.

### Verificar DMARC

Use ferramentas como `DMARC Analyzer` para verificar e monitorar os registros DMARC.

## Resumo

A implementação de SPF, DKIM e DMARC é crucial para garantir a autenticidade dos emails enviados pelo seu domínio, protegendo contra spoofing e phishing. Com a configuração adequada, você melhora a segurança dos emails e a entregabilidade, garantindo que suas notificações cheguem aos destinatários.
