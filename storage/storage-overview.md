# Overview sobre File Storage

O sistema de armazenamento de arquivos do Laravel fornece uma interface poderosa e flexível para trabalhar com arquivos em diversos sistemas de arquivos, como local, Amazon S3, Google Cloud Storage, entre outros. Com uma configuração simples e uma API unificada, você pode realizar operações de armazenamento de forma fácil e eficiente.

## Conceitos Básicos

### Drivers de Armazenamento

O Laravel suporta vários drivers de armazenamento, incluindo:

- `local`: Armazenamento de arquivos no sistema de arquivos local.
- `ftp`: Armazenamento de arquivos em um servidor FTP.
- `s3`: Armazenamento de arquivos no Amazon S3.
- `rackspace`: Armazenamento de arquivos no Rackspace Cloud Storage.
- `google`: Armazenamento de arquivos no Google Cloud Storage.

### Configuração de Drivers

A configuração dos drivers de armazenamento é feita no arquivo `config/filesystems.php`.

### Exemplo de Configuração de Driver

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

    's3' => [
        'driver' => 's3',
        'key' => env('AWS_ACCESS_KEY_ID'),
        'secret' => env('AWS_SECRET_ACCESS_KEY'),
        'region' => env('AWS_DEFAULT_REGION'),
        'bucket' => env('AWS_BUCKET'),
        'url' => env('AWS_URL'),
    ],
],
```

### API de Armazenamento

A API de armazenamento do Laravel fornece métodos para realizar operações comuns, como salvar, ler, copiar e excluir arquivos.

#### Exemplo de Uso da API de Armazenamento

```php
use Illuminate\Support\Facades\Storage;

// Salvar um arquivo
Storage::disk('local')->put('file.txt', 'Conteúdo do arquivo');

// Ler um arquivo
$content = Storage::disk('local')->get('file.txt');

// Excluir um arquivo
Storage::disk('local')->delete('file.txt');
```

## Resumo

O sistema de armazenamento de arquivos do Laravel é uma ferramenta poderosa que simplifica a manipulação de arquivos em diversos sistemas de arquivos. Com uma configuração simples e uma API unificada, você pode realizar operações de armazenamento de forma fácil e eficiente, garantindo que seus arquivos estejam seguros e acessíveis.
