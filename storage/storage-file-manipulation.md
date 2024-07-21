# Manipulação de Arquivos

Manipular arquivos é uma parte fundamental do gerenciamento de armazenamento em aplicações web. O Laravel oferece uma API poderosa para realizar operações comuns, como leitura, escrita, exclusão, cópia e movimentação de arquivos, facilitando a manipulação eficiente dos dados armazenados.

## Leitura de Arquivos

Você pode ler o conteúdo de um arquivo utilizando o método `get`.

### Exemplo de Leitura de Arquivo

```php
use Illuminate\Support\Facades\Storage;

$content = Storage::get('path/to/file.txt');
echo $content;
```

## Escrita de Arquivos

Para escrever ou substituir o conteúdo de um arquivo, utilize o método `put`.

### Exemplo de Escrita de Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::put('path/to/file.txt', 'Conteúdo do arquivo');
```

## Adicionar ao Final de um Arquivo

Para adicionar conteúdo ao final de um arquivo existente, utilize o método `append`.

### Exemplo de Adicionar ao Final de um Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::append('path/to/file.txt', 'Conteúdo adicional');
```

## Excluir Arquivos

Para excluir um arquivo, utilize o método `delete`.

### Exemplo de Exclusão de Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::delete('path/to/file.txt');
```

## Copiar Arquivos

Você pode copiar arquivos utilizando o método `copy`.

### Exemplo de Cópia de Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::copy('path/to/original.txt', 'path/to/copy.txt');
```

## Mover e Renomear Arquivos

Para mover ou renomear um arquivo, utilize o método `move`.

### Exemplo de Mover e Renomear Arquivo

```php
use Illuminate\Support\Facades\Storage;

Storage::move('path/to/original.txt', 'path/to/new_location.txt');
```

## Verificar Existência de Arquivo

Para verificar se um arquivo existe, utilize o método `exists`.

### Exemplo de Verificação de Existência de Arquivo

```php
use Illuminate\Support\Facades\Storage;

if (Storage::exists('path/to/file.txt')) {
    echo 'O arquivo existe.';
} else {
    echo 'O arquivo não existe.';
}
```

## Obter Tamanho do Arquivo

Para obter o tamanho de um arquivo, utilize o método `size`.

### Exemplo de Obter Tamanho do Arquivo

```php
use Illuminate\Support\Facades\Storage;

$size = Storage::size('path/to/file.txt');
echo 'O tamanho do arquivo é ' . $size . ' bytes.';
```

## Obter Última Modificação do Arquivo

Para obter o timestamp da última modificação de um arquivo, utilize o método `lastModified`.

### Exemplo de Obter Última Modificação do Arquivo

```php
use Illuminate\Support\Facades\Storage;

$lastModified = Storage::lastModified('path/to/file.txt');
echo 'Última modificação do arquivo foi em ' . date('Y-m-d H:i:s', $lastModified);
```

## Manipulação de Arquivos com Streams

Você pode usar streams para ler e escrever arquivos, especialmente útil para arquivos grandes.

### Exemplo de Leitura com Stream

```php
use Illuminate\Support\Facades\Storage;

$stream = Storage::readStream('path/to/file.txt');
while (($line = fgets($stream)) !== false) {
    echo $line;
}
fclose($stream);
```

### Exemplo de Escrita com Stream

```php
use Illuminate\Support\Facades\Storage;

$stream = Storage::writeStream('path/to/file.txt', fopen('path/to/local/file.txt', 'r'));
```

## Resumo

A manipulação de arquivos no Laravel é facilitada por uma API poderosa e flexível que permite realizar operações comuns, como leitura, escrita, exclusão, cópia e movimentação de arquivos. Essas operações são essenciais para gerenciar eficientemente os dados armazenados, proporcionando uma base sólida para construir funcionalidades mais avançadas na sua aplicação.
