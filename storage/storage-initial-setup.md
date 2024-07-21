# Configuração Inicial do Storage

A configuração inicial do sistema de armazenamento de arquivos do Laravel envolve a definição dos drivers de armazenamento no arquivo de configuração `filesystems.php`. Este arquivo está localizado no diretório `config` da sua aplicação Laravel e contém todas as configurações necessárias para gerenciar arquivos em diferentes sistemas de armazenamento.

## Passo 1: Configurar o Arquivo `filesystems.php`

No arquivo `config/filesystems.php`, você encontrará a configuração padrão para os drivers de armazenamento. Cada driver representa um sistema de armazenamento diferente, como o armazenamento local, FTP, Amazon S3, entre outros.

### Estrutura Básica do Arquivo

```php
return [

    'default' => env('FILESYSTEM_DRIVER', 'local'),

    'cloud' => env('FILESYSTEM_CLOUD', 's3'),

    'disks' => [

        'local' => [
            'driver' => 'local',
            'root' => storage_path('app'),
        ],

        'public' => [
            'driver' => 'local',
            'root' => storage_path('app/public'),
            'url' => env('APP_URL').'/storage',
            'visibility' => 'public',
        ],

        's3' => [
            'driver' => 's3',
            'key' => env('AWS_ACCESS_KEY_ID'),
            'secret' => env('AWS_SECRET_ACCESS_KEY'),
            'region' => env('AWS_DEFAULT_REGION'),
            'bucket' => env('AWS_BUCKET'),
            'url' => env('AWS_URL'),
            'endpoint' => env('AWS_ENDPOINT'),
        ],

    ],

];
```

### Passo 2: Configurar o Driver Padrão

O Laravel permite que você defina um driver de armazenamento padrão. Isso é configurado através da variável de ambiente `FILESYSTEM_DRIVER` no arquivo `.env`.

```env
FILESYSTEM_DRIVER=local
```

### Passo 3: Configurar o Driver de Armazenamento em Nuvem

Se você estiver usando um serviço de armazenamento em nuvem, como o Amazon S3, você também precisará configurar as credenciais de acesso no arquivo `.env`.

```env
AWS_ACCESS_KEY_ID=your-access-key-id
AWS_SECRET_ACCESS_KEY=your-secret-access-key
AWS_DEFAULT_REGION=us-east-1
AWS_BUCKET=your-bucket-name
AWS_URL=https://your-bucket-url
```

## Passo 4: Testar a Configuração

Depois de configurar o arquivo `filesystems.php` e definir as variáveis de ambiente necessárias, você pode testar a configuração do armazenamento executando algumas operações básicas, como salvar e recuperar um arquivo.

### Exemplo de Código para Teste

```php
use Illuminate\Support\Facades\Storage;

// Salvar um arquivo no armazenamento local
Storage::disk('local')->put('example.txt', 'Conteúdo do arquivo');

// Recuperar o conteúdo do arquivo
$content = Storage::disk('local')->get('example.txt');
echo $content; // Saída: Conteúdo do arquivo

// Excluir o arquivo
Storage::disk('local')->delete('example.txt');
```

## Resumo

A configuração inicial do sistema de armazenamento de arquivos do Laravel envolve a definição dos drivers de armazenamento no arquivo `filesystems.php` e a configuração das credenciais de acesso no arquivo `.env`. Com essa configuração, você pode gerenciar arquivos de forma eficiente em diferentes sistemas de armazenamento, como local e nuvem.
