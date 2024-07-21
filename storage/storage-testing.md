# Uso de Armazenamento em Testes

Testar funcionalidades relacionadas ao armazenamento de arquivos é crucial para garantir que a manipulação de arquivos ocorra conforme esperado. O Laravel oferece ferramentas robustas para testar operações de armazenamento de forma isolada e eficiente.

## Configuração de Ambiente de Teste

### Passo 1: Configurar o Driver de Teste

No arquivo `phpunit.xml`, você pode definir o driver de armazenamento a ser utilizado durante os testes.

```xml
<php>
    <env name="FILESYSTEM_DRIVER" value="testing"/>
</php>
```

### Passo 2: Configurar o Driver de Teste no `filesystems.php`

No arquivo `config/filesystems.php`, configure o driver `testing`.

```php
'disks' => [
    'testing' => [
        'driver' => 'local',
        'root' => storage_path('framework/testing/disks'),
    ],
],
```

## Utilização do Facade `Storage` nos Testes

### Passo 1: Testar o Upload de Arquivos

Você pode utilizar o facade `Storage` para simular operações de upload e verificar se os arquivos são armazenados corretamente.

### Exemplo de Teste de Upload

```php
use Illuminate\Http\UploadedFile;
use Illuminate\Support\Facades\Storage;

public function test_file_upload()
{
    Storage::fake('testing');

    $file = UploadedFile::fake()->create('document.pdf', 100);

    $response = $this->post('/upload', [
        'file' => $file,
    ]);

    $response->assertStatus(200);

    Storage::disk('testing')->assertExists('uploads/document.pdf');
}
```

### Passo 2: Testar a Exclusão de Arquivos

Você pode testar a exclusão de arquivos e verificar se o arquivo foi removido do armazenamento.

### Exemplo de Teste de Exclusão

```php
use Illuminate\Support\Facades\Storage;

public function test_file_deletion()
{
    Storage::fake('testing');

    Storage::disk('testing')->put('uploads/document.pdf', 'Conteúdo do arquivo');

    $response = $this->delete('/delete/document.pdf');

    $response->assertStatus(200);

    Storage::disk('testing')->assertMissing('uploads/document.pdf');
}
```

### Passo 3: Testar a Listagem de Arquivos

Você pode testar a listagem de arquivos e verificar se todos os arquivos esperados estão presentes.

### Exemplo de Teste de Listagem

```php
use Illuminate\Support\Facades\Storage;

public function test_file_listing()
{
    Storage::fake('testing');

    Storage::disk('testing')->put('uploads/document1.pdf', 'Conteúdo do arquivo');
    Storage::disk('testing')->put('uploads/document2.pdf', 'Conteúdo do arquivo');

    $response = $this->get('/list-uploads');

    $response->assertStatus(200)
             ->assertJson([
                 'files' => [
                     'uploads/document1.pdf',
                     'uploads/document2.pdf',
                 ],
             ]);
}
```

## Uso de Stubs e Mocks

### Passo 1: Criar Mocks para Operações de Armazenamento

Você pode utilizar stubs e mocks para simular comportamentos de operações de armazenamento, permitindo testes mais focados e isolados.

### Exemplo de Mock de Armazenamento

```php
use Illuminate\Support\Facades\Storage;

public function test_mock_storage()
{
    Storage::shouldReceive('put')
           ->once()
           ->with('uploads/document.pdf', 'Conteúdo do arquivo')
           ->andReturn(true);

    $response = $this->post('/upload', [
        'file' => UploadedFile::fake()->create('document.pdf', 100),
    ]);

    $response->assertStatus(200);
}
```

## Vantagens do Uso de Armazenamento em Testes

- **Isolamento**: Testa operações de armazenamento sem afetar o sistema de arquivos real.
- **Consistência**: Garante que os testes sejam executados em um ambiente controlado.
- **Facilidade de Verificação**: Permite verificar a existência, ausência e conteúdo de arquivos armazenados.

## Resumo

Testar funcionalidades de armazenamento de arquivos no Laravel é facilitado pela capacidade de simular operações de armazenamento utilizando drivers de teste e o facade `Storage`. Com uma configuração adequada, você pode garantir que todas as operações de upload, exclusão e listagem de arquivos ocorram conforme esperado, aumentando a confiabilidade e a robustez da sua aplicação.
