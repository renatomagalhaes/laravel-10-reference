# Upload de Imagem com Conversão para WebP

A conversão de imagens para o formato WebP pode melhorar significativamente o desempenho de sua aplicação, pois o WebP oferece compressão superior para imagens na web. O Laravel, juntamente com a biblioteca `Intervention Image`, facilita a conversão de imagens para WebP durante o processo de upload.

## Passo 1: Instalar a Biblioteca de Manipulação de Imagens

Para manipular imagens, incluindo a conversão para WebP, você pode usar a biblioteca `intervention/image`.

### Instalar a Biblioteca

```bash
composer require intervention/image
```

### Configurar o Serviço de Provedor

Adicione o provedor de serviço ao arquivo `config/app.php`.

```php
'providers' => [
    Intervention\Image\ImageServiceProvider::class,
],

'aliases' => [
    'Image' => Intervention\Image\Facades\Image::class,
],
```

## Passo 2: Configurar o Formulário de Upload

Crie um formulário HTML para permitir o upload de imagens.

```html
<form action="/upload-image-webp" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="image">
    <button type="submit">Upload</button>
</form>
```

## Passo 3: Processar e Converter a Imagem no Controlador

No controlador, valide o upload da imagem, processe e converta para WebP utilizando a biblioteca `Intervention Image`.

### Exemplo de Código do Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;
use Intervention\Image\Facades\Image;

class ImageController extends Controller
{
    public function upload(Request $request)
    {
        $request->validate([
            'image' => 'required|image|max:10240', // Máximo de 10 MB
        ]);

        $image = $request->file('image');
        $imageName = pathinfo($image->getClientOriginalName(), PATHINFO_FILENAME) . '.webp';
        $imagePath = storage_path('app/uploads/' . $imageName);

        // Processar e converter a imagem para WebP
        Image::make($image)->encode('webp', 90)->save($imagePath);

        // Armazenar a imagem no disco
        Storage::put('uploads/' . $imageName, file_get_contents($imagePath));

        return response()->json(['path' => Storage::url('uploads/' . $imageName)]);
    }
}
```

## Passo 4: Testar o Upload e Conversão

Acesse a rota configurada e faça o upload de uma imagem para testar o processo de conversão e armazenamento.

### Exemplo de Rota

```php
use App\Http\Controllers\ImageController;

Route::post('/upload-image-webp', [ImageController::class, 'upload']);
```

## Vantagens do Formato WebP

- **Compressão Superior**: Oferece compressão melhorada em comparação com JPEG e PNG, resultando em tamanhos de arquivo menores.
- **Qualidade de Imagem**: Mantém alta qualidade de imagem mesmo com tamanhos de arquivo reduzidos.
- **Suporte Amplo**: Suportado pela maioria dos navegadores modernos.

## Resumo

Converter imagens para o formato WebP durante o processo de upload no Laravel pode melhorar significativamente o desempenho da sua aplicação. Utilizando a biblioteca `Intervention Image`, você pode facilmente processar e converter imagens para WebP, proporcionando uma melhor experiência para os usuários com tempos de carregamento mais rápidos e menor uso de largura de banda.
