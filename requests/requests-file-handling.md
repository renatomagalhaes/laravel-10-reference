# Manipulação de Arquivos em Requisições

A manipulação de arquivos é uma funcionalidade comum em muitas aplicações web, como upload de imagens, documentos, e outros tipos de arquivos. O Laravel facilita o gerenciamento de arquivos enviados através de requisições HTTP.

## Upload de Arquivos

### Passo 1: Configurar o Formulário de Upload

Para permitir o upload de arquivos, configure o formulário HTML com o atributo `enctype="multipart/form-data"`.

#### Exemplo de Formulário de Upload

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="file">
    <button type="submit">Upload</button>
</form>
```

### Passo 2: Processar o Upload no Controlador

No controlador, você pode acessar o arquivo enviado utilizando o método `file` do objeto `Request`.

#### Exemplo de Processamento de Upload

```php
use Illuminate\Http\Request;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240', // Máximo de 10 MB
    ]);

    $path = $request->file('file')->store('uploads');

    return response()->json(['path' => $path]);
});
```

## Validação de Arquivos

### Passo 1: Definir Regras de Validação

Você pode validar arquivos com regras específicas, como tipo de arquivo e tamanho máximo.

#### Exemplo de Validação de Arquivos

```php
Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|mimes:jpg,png,pdf|max:2048', // Máximo de 2 MB
    ]);

    $path = $request->file('file')->store('uploads');

    return response()->json(['path' => $path]);
});
```

## Armazenamento de Arquivos

### Passo 1: Utilizar o Storage do Laravel

O Laravel oferece uma API de armazenamento que facilita o gerenciamento de arquivos em diferentes sistemas de arquivos.

#### Exemplo de Armazenamento de Arquivo

```php
use Illuminate\Support\Facades\Storage;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240',
    ]);

    $path = $request->file('file')->store('uploads', 'public');

    return response()->json(['path' => Storage::url($path)]);
});
```

## Manipulação de Arquivos

### Passo 1: Obter o Caminho do Arquivo

Você pode obter o caminho do arquivo armazenado para processamento posterior.

```php
$path = $request->file('file')->storeAs('uploads', $filename);
```

### Passo 2: Mover Arquivos

Você pode mover arquivos entre diferentes diretórios de armazenamento.

```php
use Illuminate\Support\Facades\Storage;

Storage::move('old_path/file.txt', 'new_path/file.txt');
```

### Passo 3: Excluir Arquivos

Você pode excluir arquivos armazenados.

```php
use Illuminate\Support\Facades\Storage;

Storage::delete('path/to/file.txt');
```

## Baixar Arquivos

### Passo 1: Enviar Arquivos para Download

Você pode enviar arquivos armazenados para download utilizando a função `download` do Storage.

```php
use Illuminate\Support\Facades\Storage;

Route::get('/download/{file}', function ($file) {
    return Storage::download('uploads/' . $file);
});
```

## Resumo

A manipulação de arquivos no Laravel é facilitada pela API de armazenamento integrada, que oferece métodos para upload, validação, armazenamento, movimentação e exclusão de arquivos. Com essas ferramentas, você pode gerenciar arquivos de forma eficiente e segura, proporcionando uma experiência robusta para os usuários da sua aplicação.
