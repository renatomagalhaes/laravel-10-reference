# Link Simbólico para Armazenamento

Links simbólicos são atalhos que apontam para diretórios ou arquivos específicos no sistema de arquivos. No Laravel, os links simbólicos são frequentemente usados para tornar os arquivos armazenados acessíveis publicamente sem mover os arquivos reais para o diretório público. Isso é especialmente útil para armazenar uploads de usuários e outros arquivos de mídia.

## Passo 1: Criar Link Simbólico

O Laravel fornece um comando Artisan que cria um link simbólico do diretório de armazenamento para o diretório público.

### Exemplo de Comando para Criar Link Simbólico

```bash
php artisan storage:link
```

### Resultado do Comando

Este comando cria um link simbólico chamado `storage` no diretório `public`, apontando para o diretório `storage/app/public`.

## Passo 2: Configurar Arquivo `filesystems.php`

Certifique-se de que o disco `public` esteja configurado corretamente no arquivo `config/filesystems.php`.

### Exemplo de Configuração de Disco Público

```php
'disks' => [
    'local' => [
        'driver' => 'local',
        'root' => storage_path('app'),
    ],

    'public' => [
        'driver' => 'local',
        'root' => storage_path('app/public'),
        'url' => env('APP_URL') . '/storage',
        'visibility' => 'public',
    ],
],
```

## Passo 3: Armazenar Arquivos no Disco Público

Para armazenar arquivos no disco público e torná-los acessíveis através do link simbólico, utilize o método `store` com o disco `public`.

### Exemplo de Armazenamento de Arquivo no Disco Público

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240', // Máximo de 10 MB
    ]);

    $path = $request->file('file')->store('uploads', 'public');

    return response()->json(['path' => Storage::url($path)]);
});
```

## Passo 4: Acessar Arquivos Públicos

Depois de criar o link simbólico e armazenar arquivos no disco público, você pode acessar os arquivos através da URL configurada.

### Exemplo de Acesso a Arquivo Público

```php
$url = Storage::url('uploads/example.jpg');
echo '<img src="' . $url . '" alt="Example Image">';
```

## Vantagens de Usar Links Simbólicos

- **Segurança**: Mantém os arquivos fora do diretório público do servidor, reduzindo o risco de exposição acidental.
- **Facilidade de Acesso**: Permite acesso fácil aos arquivos através de URLs simples.
- **Organização**: Mantém os arquivos organizados no diretório de armazenamento enquanto os torna acessíveis publicamente.

## Resumo

Os links simbólicos são uma ferramenta poderosa no Laravel para tornar arquivos armazenados acessíveis publicamente sem mover os arquivos reais para o diretório público. Com a configuração correta e o uso do comando Artisan `storage:link`, você pode gerenciar eficientemente o armazenamento e o acesso a arquivos em sua aplicação.
