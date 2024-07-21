# Trabalhando com Cloudinary

Cloudinary é um serviço de gerenciamento de mídia que oferece uma solução robusta para o upload, armazenamento, manipulação e entrega de imagens e vídeos. Integrar o Laravel com Cloudinary permite aproveitar esses recursos avançados para otimizar a gestão de mídia em sua aplicação.

## Passo 1: Instalar o SDK do Cloudinary

Se você ainda não instalou o SDK do Cloudinary, pode fazê-lo via Composer.

### Instalar o SDK

```bash
composer require cloudinary/cloudinary_php
```

## Passo 2: Configurar as Credenciais no Arquivo `.env`

Adicione suas credenciais do Cloudinary no arquivo `.env`.

```env
CLOUDINARY_URL=cloudinary://your_api_key:your_api_secret@your_cloud_name
```

## Passo 3: Configurar o Cloudinary no Laravel

Crie um serviço para integrar o Cloudinary ao Laravel.

### Exemplo de Serviço Cloudinary

```php
namespace App\Services;

use Cloudinary\Cloudinary;

class CloudinaryService
{
    protected $cloudinary;

    public function __construct()
    {
        $this->cloudinary = new Cloudinary(env('CLOUDINARY_URL'));
    }

    public function upload($filePath, $options = [])
    {
        return $this->cloudinary->uploadApi()->upload($filePath, $options);
    }

    public function getUrl($publicId, $options = [])
    {
        return $this->cloudinary->image($publicId)->toUrl($options);
    }
}
```

## Passo 4: Configurar o Formulário de Upload

Crie um formulário HTML para permitir o upload de imagens.

```html
<form action="/upload-cloudinary" method="POST" enctype="multipart/form-data">
    @csrf
    <input type="file" name="image">
    <button type="submit">Upload</button>
</form>
```

## Passo 5: Processar o Upload e Converter Imagens no Controlador

No controlador, valide o upload da imagem, processe e envie para o Cloudinary.

### Exemplo de Código do Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use App\Services\CloudinaryService;

class ImageController extends Controller
{
    protected $cloudinary;

    public function __construct(CloudinaryService $cloudinary)
    {
        $this->cloudinary = $cloudinary;
    }

    public function upload(Request $request)
    {
        $request->validate([
            'image' => 'required|image|max:10240', // Máximo de 10 MB
        ]);

        $imagePath = $request->file('image')->getRealPath();
        $uploadResult = $this->cloudinary->upload($imagePath, [
            'folder' => 'uploads',
            'transformation' => [
                ['width' => 500, 'height' => 500, 'crop' => 'limit']
            ]
        ]);

        return response()->json(['url' => $uploadResult['secure_url']]);
    }
}
```

## Passo 6: Testar o Upload e Conversão

Acesse a rota configurada e faça o upload de uma imagem para testar o processo de conversão e armazenamento.

### Exemplo de Rota

```php
use App\Http\Controllers\ImageController;

Route::post('/upload-cloudinary', [ImageController::class, 'upload']);
```

## Vantagens do Cloudinary

- **Manipulação de Imagens**: Permite transformar imagens durante o upload (redimensionar, cortar, aplicar filtros, etc.).
- **Entrega Otimizada**: Serve as imagens otimizadas para diferentes dispositivos e redes.
- **Gerenciamento Centralizado**: Oferece um painel para gerenciar todas as suas mídias.

## Resumo

Integrar o Laravel com o Cloudinary permite que você aproveite um serviço avançado de gerenciamento de mídia para otimizar o upload, armazenamento, manipulação e entrega de imagens. Utilizando o SDK do Cloudinary e configurando adequadamente sua aplicação Laravel, você pode melhorar significativamente a performance e a flexibilidade da gestão de mídia em sua aplicação.
