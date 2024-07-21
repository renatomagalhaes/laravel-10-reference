# Upload de Arquivos

O upload de arquivos é uma funcionalidade comum em muitas aplicações web. O Laravel fornece uma maneira fácil e eficiente de lidar com uploads de arquivos, permitindo que você valide, armazene e manipule arquivos enviados pelos usuários.

## Passo 1: Configurar o Formulário de Upload

Para permitir o upload de arquivos, configure o formulário HTML com o atributo `enctype="multipart/form-data"`.

### Exemplo de Formulário de Upload

```html
<form action="/upload" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="file">
    <button type="submit">Upload</button>
</form>
```

## Passo 2: Processar o Upload no Controlador

No controlador, você pode acessar o arquivo enviado utilizando o método `file` do objeto `Request` e armazená-lo utilizando a API de armazenamento do Laravel.

### Exemplo de Processamento de Upload

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240', // Máximo de 10 MB
    ]);

    $path = $request->file('file')->store('uploads');

    return response()->json(['path' => $path]);
});
```

## Passo 3: Validar Arquivos

Você pode validar os arquivos enviados utilizando as regras de validação do Laravel.

### Exemplo de Validação de Arquivos

```php
$request->validate([
    'file' => 'required|mimes:jpg,png,pdf|max:2048', // Máximo de 2 MB
]);
```

## Passo 4: Gerenciar o Caminho do Arquivo

Após o upload, você pode obter o caminho do arquivo armazenado para uso posterior.

```php
$path = $request->file('file')->store('uploads');
```

## Upload de Imagens com Conversão para WebP

### Passo 1: Instalar a Biblioteca de Manipulação de Imagens

Para manipular imagens, como converter para o formato WebP, você pode usar a biblioteca `intervention/image`.

```bash
composer require intervention/image
```

### Passo 2: Processar e Converter a Imagem

Utilize a biblioteca `Intervention Image` para processar e converter a imagem para o formato WebP.

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;
use Intervention\Image\Facades\Image;

Route::post('/upload', function (Request $request) {
    $request->validate([
        'file' => 'required|image|max:10240', // Máximo de 10 MB
    ]);

    $image = $request->file('file');
    $imageName = pathinfo($image->getClientOriginalName(), PATHINFO_FILENAME) . '.webp';
    $imagePath = storage_path('app/uploads/' . $imageName);

    Image::make($image)->encode('webp', 90)->save($imagePath);

    Storage::put('uploads/' . $imageName, file_get_contents($imagePath));

    return response()->json(['path' => Storage::url('uploads/' . $imageName)]);
});
```

## Resumo

O upload de arquivos no Laravel é uma tarefa simples, facilitada pela API de armazenamento robusta e ferramentas de validação. Além do upload básico, você pode realizar operações mais avançadas, como a conversão de imagens para formatos otimizados como WebP, utilizando bibliotecas adicionais como o `Intervention Image`. Essas capacidades permitem que sua aplicação lide eficientemente com uploads de arquivos, proporcionando uma melhor experiência ao usuário.
