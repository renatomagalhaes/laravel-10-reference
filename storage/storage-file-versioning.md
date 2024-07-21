# Versionamento de Arquivos

O versionamento de arquivos é uma prática útil para manter diferentes versões de um arquivo, permitindo rastrear mudanças ao longo do tempo e restaurar versões anteriores se necessário. No Laravel, você pode implementar o versionamento de arquivos de várias maneiras, incluindo a criação de novos nomes de arquivos ou o uso de diretórios de versões.

## Implementação de Versionamento de Arquivos

### Passo 1: Adicionar um Sufixo de Versão ao Nome do Arquivo

Uma abordagem simples para versionar arquivos é adicionar um sufixo de versão ao nome do arquivo. Por exemplo, se o arquivo original for `document.txt`, a primeira versão pode ser `document_v1.txt`, a segunda versão `document_v2.txt`, e assim por diante.

### Exemplo de Código para Adicionar um Sufixo de Versão

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

Route::post('/upload-versioned', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240',
    ]);

    $file = $request->file('file');
    $filename = pathinfo($file->getClientOriginalName(), PATHINFO_FILENAME);
    $extension = $file->getClientOriginalExtension();
    
    $version = 1;
    $versionedFilename = $filename . '_v' . $version . '.' . $extension;

    while (Storage::exists('uploads/' . $versionedFilename)) {
        $version++;
        $versionedFilename = $filename . '_v' . $version . '.' . $extension;
    }

    $path = $file->storeAs('uploads', $versionedFilename);

    return response()->json(['path' => $path]);
});
```

### Passo 2: Usar Diretórios para Versões

Outra abordagem é armazenar cada versão do arquivo em um diretório separado. Isso pode ser útil para manter os arquivos organizados e facilitar a recuperação de versões específicas.

### Exemplo de Código para Usar Diretórios de Versões

```php
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

Route::post('/upload-versioned-dir', function (Request $request) {
    $request->validate([
        'file' => 'required|file|max:10240',
    ]);

    $file = $request->file('file');
    $filename = $file->getClientOriginalName();
    
    $version = 1;
    $versionedDir = 'uploads/' . $filename . '/v' . $version;

    while (Storage::exists($versionedDir)) {
        $version++;
        $versionedDir = 'uploads/' . $filename . '/v' . $version;
    }

    $path = $file->storeAs($versionedDir, $filename);

    return response()->json(['path' => $path]);
});
```

## Restauração de Versões Anteriores

Para restaurar uma versão anterior de um arquivo, você pode listar as versões disponíveis e copiar o arquivo da versão desejada para o local principal.

### Exemplo de Código para Listar e Restaurar Versões

```php
Route::get('/list-versions/{filename}', function ($filename) {
    $directories = Storage::directories('uploads/' . $filename);
    return response()->json($directories);
});

Route::post('/restore-version', function (Request $request) {
    $request->validate([
        'filename' => 'required|string',
        'version' => 'required|string',
    ]);

    $versionedDir = 'uploads/' . $request->input('filename') . '/' . $request->input('version');
    $files = Storage::files($versionedDir);

    foreach ($files as $file) {
        $baseFile = basename($file);
        Storage::copy($file, 'uploads/' . $baseFile);
    }

    return response()->json(['message' => 'Versão restaurada com sucesso']);
});
```

## Vantagens do Versionamento de Arquivos

- **Rastreamento de Mudanças**: Permite rastrear mudanças ao longo do tempo e entender o histórico de um arquivo.
- **Recuperação de Versões**: Facilita a restauração de versões anteriores em caso de erros ou perda de dados.
- **Organização**: Mantém os arquivos organizados, especialmente quando múltiplas versões de um arquivo são necessárias.

## Resumo

O versionamento de arquivos no Laravel pode ser implementado de várias maneiras, incluindo a adição de sufixos de versão aos nomes dos arquivos ou o uso de diretórios de versões. Essas práticas ajudam a manter um histórico claro das mudanças nos arquivos e facilitam a recuperação de versões anteriores quando necessário, melhorando a organização e a gestão dos arquivos na sua aplicação.
