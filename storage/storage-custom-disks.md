# Uso de Discos Personalizados

O Laravel permite a configuração de discos personalizados para armazenar arquivos em diferentes locais e com diferentes configurações. Isso é útil para organizar melhor os arquivos, utilizar diferentes sistemas de armazenamento ou aplicar configurações específicas a determinados conjuntos de arquivos.

## Passo 1: Configurar o Disco Personalizado

No arquivo `config/filesystems.php`, adicione a configuração para o disco personalizado. 

### Exemplo de Configuração de Disco Personalizado

```php
'disks' => [
    'custom' => [
        'driver' => 'local',
        'root' => storage_path('app/custom'),
        'url' => env('APP_URL') . '/storage/custom',
        'visibility' => 'public',
    ],
],
```

### Exemplo de Configuração para um Disco S3 Personalizado

```php
'disks' => [
    's3_custom' => [
        'driver' => 's3',
        'key' => env('AWS_CUSTOM_ACCESS_KEY_ID'),
        'secret' => env('AWS_CUSTOM_SECRET_ACCESS_KEY'),
        'region' => env('AWS_CUSTOM_DEFAULT_REGION'),
        'bucket' => env('AWS_CUSTOM_BUCKET'),
        'url' => env('AWS_CUSTOM_URL'),
        'endpoint' => env('AWS_CUSTOM_ENDPOINT'),
    ],
],
```

## Passo 2: Utilizar o Disco Personalizado

Depois de configurar o disco personalizado, você pode utilizá-lo na sua aplicação como qualquer outro disco.

### Exemplo de Uso do Disco Personalizado

#### Salvar um Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::disk('custom')->put('example.txt', 'Conteúdo do arquivo');
```

#### Ler um Arquivo

```php
$content = Storage::disk('custom')->get('example.txt');
echo $content;
```

#### Excluir um Arquivo

```php
Storage::disk('custom')->delete('example.txt');
```

## Passo 3: Gerenciar Visibilidade dos Arquivos

Você pode definir a visibilidade dos arquivos no disco personalizado, assim como faria com qualquer outro disco.

### Exemplo de Definir Visibilidade

```php
Storage::disk('custom')->put('example.txt', 'Conteúdo do arquivo', 'public');
```

### Exemplo de Verificar e Alterar Visibilidade

```php
$visibility = Storage::disk('custom')->getVisibility('example.txt');
echo $visibility; // Saída: public ou private

Storage::disk('custom')->setVisibility('example.txt', 'private');
```

## Vantagens de Usar Discos Personalizados

- **Flexibilidade**: Permite configurar diferentes locais e métodos de armazenamento para diferentes tipos de arquivos.
- **Organização**: Ajuda a manter os arquivos organizados em diferentes discos com diferentes propósitos.
- **Configurações Específicas**: Permite aplicar configurações específicas, como diferentes chaves de API, regiões ou permissões.

## Resumo

O uso de discos personalizados no Laravel é uma ferramenta poderosa para gerenciar arquivos em diferentes locais e com diferentes configurações. Com uma configuração simples no arquivo `filesystems.php`, você pode criar e utilizar discos personalizados para atender às necessidades específicas da sua aplicação, melhorando a organização e a flexibilidade do armazenamento de arquivos.
