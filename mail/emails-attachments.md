# Envio de Anexos

O envio de anexos é uma funcionalidade comum em emails, permitindo que arquivos sejam enviados juntamente com a mensagem. O Laravel facilita a adição de anexos aos emails utilizando a classe Mailable.

## Passo 1: Criar uma Classe Mailable com Anexos

Use o comando Artisan para criar uma classe Mailable:

```bash
php artisan make:mail AttachmentMail
```

### Exemplo de Classe Mailable com Anexos

No arquivo `app/Mail/AttachmentMail.php`, defina a estrutura do email e adicione os anexos:

```php
namespace App\Mail;

use Illuminate\Bus\Queueable;
use Illuminate\Mail\Mailable;
use Illuminate\Queue\SerializesModels;

class AttachmentMail extends Mailable
{
    use Queueable, SerializesModels;

    public $details;
    public $attachment;

    public function __construct($details, $attachment)
    {
        $this->details = $details;
        $this->attachment = $attachment;
    }

    public function build()
    {
        return $this->view('emails.attachment')
                    ->subject('Assunto do Email')
                    ->attach($this->attachment, [
                        'as' => 'nome_do_anexo.pdf',
                        'mime' => 'application/pdf',
                    ]);
    }
}
```

## Passo 2: Criar a View do Email

Crie um arquivo de view para o corpo do email em `resources/views/emails/attachment.blade.php`:

```blade
<!DOCTYPE html>
<html>
<head>
    <title>{{ $details['title'] }}</title>
</head>
<body>
    <h1>{{ $details['body'] }}</h1>
    <p>Obrigado!</p>
</body>
</html>
```

## Passo 3: Enviar o Email com Anexo

No controlador, envie o email utilizando a classe Mailable com o anexo:

```php
use App\Mail\AttachmentMail;
use Illuminate\Support\Facades\Mail;

class EmailController extends Controller
{
    public function sendAttachmentEmail(Request $request)
    {
        $details = [
            'title' => 'Título do Email',
            'body' => 'Este é o corpo do email.'
        ];

        $attachment = $request->file('attachment')->getRealPath();

        Mail::to('destinatario@example.com')->send(new AttachmentMail($details, $attachment));

        return response()->json(['message' => 'Email com anexo enviado com sucesso!']);
    }
}
```

## Passo 4: Validar e Processar Anexos

### Validação de Anexos

No controlador, valide o anexo antes de enviar o email:

```php
public function sendAttachmentEmail(Request $request)
{
    $request->validate([
        'attachment' => 'required|file|mimes:pdf|max:2048',
    ]);

    $details = [
        'title' => 'Título do Email',
        'body' => 'Este é o corpo do email.'
    ];

    $attachment = $request->file('attachment')->getRealPath();

    Mail::to('destinatario@example.com')->send(new AttachmentMail($details, $attachment));

    return response()->json(['message' => 'Email com anexo enviado com sucesso!']);
}
```

## Benefícios do Envio de Anexos

- **Versatilidade**: Permite enviar diversos tipos de arquivos (PDFs, imagens, documentos, etc.).
- **Facilidade**: Simplifica a comunicação, permitindo que informações adicionais sejam anexadas ao email.
- **Segurança**: O Laravel facilita a validação e o processamento seguro de anexos.

## Resumo

O envio de anexos no Laravel é um processo simples e direto, utilizando a classe Mailable. Com a configuração correta, você pode adicionar anexos aos seus emails de forma eficiente, garantindo que as informações adicionais sejam entregues juntamente com a mensagem principal.
