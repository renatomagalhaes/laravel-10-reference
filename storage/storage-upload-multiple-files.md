# Upload de Múltiplos Arquivos

O upload de múltiplos arquivos permite que os usuários enviem vários arquivos em uma única requisição. O Laravel facilita essa funcionalidade, proporcionando uma maneira eficiente de gerenciar e armazenar múltiplos arquivos de uma só vez.

## Passo 1: Configurar o Formulário de Upload

Para permitir o upload de múltiplos arquivos, configure o formulário HTML com o atributo `multiple`.

### Exemplo de Formulário de Upload

```html
<form action="/upload-multiple" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="files[]" multiple>
    <button type="submit">Upload</button>
</form>
```

## Passo 2: Processar o Upload no Controlador

No controlador, você pode acessar os arquivos enviados utilizando o método `file` do objeto `Request` e armazená-los utilizando a API de armazenamento do Laravel.

### Exemplo de Processamento de Upload de Múltiplos Arquivos

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

Route::post('/upload-multiple', function (Request $request) {
    $request->validate([
        'files.*' => 'required|file|max:10240', // Cada arquivo pode ter até 10 MB
    ]);

    $paths = [];

    foreach ($request->file('files') as $file) {
        $path = $file->store('uploads');
        $paths[] = $path;
    }

    return response()->json(['paths' => $paths]);
});
```

## Passo 3: Validar Arquivos

Você pode validar cada arquivo individualmente utilizando as regras de validação do Laravel.

### Exemplo de Validação de Múltiplos Arquivos

```php
$request->validate([
    'files.*' => 'required|mimes:jpg,png,pdf|max:2048', // Cada arquivo pode ter até 2 MB
]);
```

## Upload de Imagens com Conversão para WebP em Múltiplos Arquivos

### Passo 1: Processar e Converter as Imagens

Utilize a biblioteca `Intervention Image` para processar e converter as imagens para o formato WebP.

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;
use Intervention\Image\Facades\Image;

Route::post('/upload-multiple', function (Request $request) {
    $request->validate([
        'files.*' => 'required|image|max:10240', // Cada arquivo pode ter até 10 MB
    ]);

    $paths = [];

    foreach ($request->file('files') as $file) {
        $imageName = pathinfo($file->getClientOriginalName(), PATHINFO_FILENAME) . '.webp';
        $imagePath = storage_path('app/uploads/' . $imageName);

        Image::make($file)->encode('webp', 90)->save($imagePath);

        Storage::put('uploads/' . $imageName, file_get_contents($imagePath));
        $paths[] = Storage::url('uploads/' . $imageName);
    }

    return response()->json(['paths' => $paths]);
});
```

## Resumo

O upload de múltiplos arquivos no Laravel permite que os usuários enviem vários arquivos de uma vez, simplificando o processo e melhorando a experiência do usuário. Com a API de armazenamento robusta do Laravel, você pode validar, armazenar e manipular múltiplos arquivos de forma eficiente, incluindo a conversão de imagens para formatos otimizados como WebP. Essa funcionalidade é essencial para aplicações que lidam com grandes volumes de dados enviados pelos usuários.
