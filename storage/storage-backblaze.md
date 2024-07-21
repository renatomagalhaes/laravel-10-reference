# Trabalhando com o Backblaze

O Backblaze B2 é uma solução de armazenamento em nuvem acessível e confiável que pode ser facilmente integrada ao Laravel. Com Backblaze, você pode armazenar grandes quantidades de dados de maneira econômica e segura, aproveitando as capacidades de escalabilidade e durabilidade do serviço.

## Configuração do Backblaze B2

### Passo 1: Instalar o SDK do Backblaze

Se você ainda não instalou o SDK do Backblaze, pode fazê-lo via Composer.

```bash
composer require backblaze/b2-sdk-php
```

### Passo 2: Configurar as Credenciais no Arquivo `.env`

Adicione suas credenciais do Backblaze B2 no arquivo `.env`.

```env
B2_ACCOUNT_ID=your-account-id
B2_APPLICATION_KEY=your-application-key
B2_BUCKET_NAME=your-bucket-name
B2_BUCKET_ID=your-bucket-id
B2_ENDPOINT=https://api.backblazeb2.com
```

### Passo 3: Configurar o Driver Backblaze no Arquivo `filesystems.php`

No arquivo `config/filesystems.php`, configure o driver Backblaze.

```php
'disks' => [
    'b2' => [
        'driver' => 'b2',
        'accountId' => env('B2_ACCOUNT_ID'),
        'applicationKey' => env('B2_APPLICATION_KEY'),
        'bucketId' => env('B2_BUCKET_ID'),
        'bucketName' => env('B2_BUCKET_NAME'),
    ],
],
```

## Operações Comuns com Backblaze

### Salvar Arquivos no Backblaze

Você pode salvar arquivos diretamente no Backblaze utilizando o método `put`.

```php
use Illuminate\Support\Facades\Storage;

Storage::disk('b2')->put('file.txt', 'Conteúdo do arquivo');
```

### Ler Arquivos do Backblaze

Você pode ler arquivos armazenados no Backblaze utilizando o método `get`.

```php
$content = Storage::disk('b2')->get('file.txt');
echo $content;
```

### Excluir Arquivos do Backblaze

Para excluir arquivos do Backblaze, utilize o método `delete`.

```php
Storage::disk('b2')->delete('file.txt');
```

### Obter URL de Arquivo no Backblaze

Você pode obter a URL de um arquivo armazenado no Backblaze utilizando o método `url`.

```php
$url = Storage::disk('b2')->url('file.txt');
echo $url;
```

## Geração de URLs Temporárias

### Passo 1: Gerar URLs Temporárias

Para fornecer acesso temporário a arquivos privados, você pode gerar URLs temporárias.

```php
$url = Storage::disk('b2')->temporaryUrl(
    'file.txt', now()->addMinutes(5)
);
echo $url;
```

## Configurações Avançadas

### Definir Visibilidade dos Arquivos

Você pode definir a visibilidade dos arquivos como `public` ou `private` ao armazená-los.

```php
Storage::disk('b2')->put('file.txt', 'Conteúdo do arquivo', 'public');
```

### Gerenciar Permissões de Arquivos

Utilize métodos adicionais para gerenciar permissões e visibilidade de arquivos existentes.

```php
Storage::disk('b2')->setVisibility('file.txt', 'private');
$visibility = Storage::disk('b2')->getVisibility('file.txt');
echo $visibility; // Saída: private
```

## Resumo

Integrar o Laravel com o Backblaze B2 permite que você aproveite uma solução de armazenamento em nuvem acessível e confiável. Com configurações simples e a poderosa API de armazenamento do Laravel, você pode realizar operações comuns de armazenamento, leitura, exclusão e gerenciamento de arquivos de forma eficiente, além de fornecer URLs temporárias para acesso controlado aos arquivos.
