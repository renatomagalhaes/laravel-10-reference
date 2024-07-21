# Armazenamento Local

O armazenamento local é a forma mais simples e direta de armazenar arquivos na sua aplicação Laravel. Utilizando o disco local, você pode gerenciar arquivos diretamente no sistema de arquivos do servidor onde sua aplicação está hospedada.

## Configuração do Armazenamento Local

### Passo 1: Configurar o Disco Local

No arquivo `config/filesystems.php`, o disco local é configurado por padrão. Ele armazena arquivos na pasta `storage/app`.

```php
'local' => [
    'driver' => 'local',
    'root' => storage_path('app'),
],
```

### Passo 2: Utilizar o Disco Local

Para utilizar o disco local, você pode chamar o método `Storage::disk('local')` e realizar operações de leitura e escrita de arquivos.

#### Exemplo de Salvar um Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::disk('local')->put('example.txt', 'Conteúdo do arquivo');
```

#### Exemplo de Ler um Arquivo

```php
$content = Storage::disk('local')->get('example.txt');
echo $content; // Saída: Conteúdo do arquivo
```

#### Exemplo de Excluir um Arquivo

```php
Storage::disk('local')->delete('example.txt');
```

## Manipulação de Diretórios

Você pode criar, listar e excluir diretórios utilizando o disco local.

### Criar Diretório

```php
Storage::disk('local')->makeDirectory('nova_pasta');
```

### Listar Diretórios e Arquivos

```php
$directories = Storage::disk('local')->directories();
$files = Storage::disk('local')->files();
```

### Excluir Diretório

```php
Storage::disk('local')->deleteDirectory('nova_pasta');
```

## Gerenciamento de Arquivos

Além de salvar e ler arquivos, você pode realizar operações adicionais, como copiar e mover arquivos.

### Copiar Arquivo

```php
Storage::disk('local')->copy('example.txt', 'copy_example.txt');
```

### Mover Arquivo

```php
Storage::disk('local')->move('example.txt', 'moved_example.txt');
```

## Visibilidade dos Arquivos

Você pode definir a visibilidade dos arquivos como `public` ou `private`.

### Definir Visibilidade

```php
Storage::disk('local')->put('example.txt', 'Conteúdo do arquivo', 'public');
```

### Verificar Visibilidade

```php
$visibility = Storage::disk('local')->getVisibility('example.txt');
echo $visibility; // Saída: public ou private
```

### Alterar Visibilidade

```php
Storage::disk('local')->setVisibility('example.txt', 'private');
```

## Resumo

O armazenamento local no Laravel é uma maneira eficiente e direta de gerenciar arquivos no sistema de arquivos do servidor. Com uma configuração simples e uma API poderosa, você pode realizar operações de leitura, escrita, cópia, movimento e exclusão de arquivos, além de gerenciar diretórios e definir a visibilidade dos arquivos.
