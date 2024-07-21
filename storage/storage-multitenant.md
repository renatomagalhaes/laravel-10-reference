# Cenário de Multitenant

Multitenancy é uma abordagem eficaz para servir múltiplos clientes ou usuários dentro da mesma aplicação, mantendo os dados isolados e proporcionando uma experiência personalizada. No contexto de armazenamento de arquivos, isso significa garantir que cada tenant tenha seu próprio espaço de armazenamento seguro e independente.

## Abordagens para Multitenancy

### 1. Diretórios Separados por Tenant

Uma abordagem simples e direta é criar diretórios separados para cada tenant. Isso mantém os arquivos isolados e organizados.

#### Exemplo de Estrutura de Diretórios

```
storage/app/tenants/
    tenant1/
        file1.txt
        file2.jpg
    tenant2/
        file1.pdf
        file2.png
```

### 2. Uso de Prefixos de Tenant

Outra abordagem é usar prefixos baseados no identificador do tenant para diferenciar os arquivos armazenados.

#### Exemplo de Prefixos de Tenant

```php
$tenantId = auth()->user()->tenant_id;
Storage::put("tenants/{$tenantId}/file.txt", 'Conteúdo do arquivo');
```

## Implementação de Multitenancy no Laravel

### Passo 1: Configurar Middleware para Identificar o Tenant

Crie um middleware para identificar e definir o tenant ativo para cada requisição.

#### Exemplo de Middleware

```php
namespace App\Http\Middleware;

use Closure;

class SetTenant
{
    public function handle($request, Closure $next)
    {
        // Supondo que o tenant_id esteja na sessão
        $tenantId = session('tenant_id');
        if ($tenantId) {
            // Definir o tenant ativo
            app()->instance('tenant', $tenantId);
        }

        return $next($request);
    }
}
```

### Passo 2: Utilizar o Tenant para Armazenamento de Arquivos

No controlador, utilize o tenant ativo para organizar os arquivos no armazenamento.

#### Exemplo de Código do Controlador

```php
namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Storage;

class TenantFileController extends Controller
{
    public function upload(Request $request)
    {
        $request->validate([
            'file' => 'required|file|max:10240', // Máximo de 10 MB
        ]);

        $tenantId = app('tenant');
        $filePath = "tenants/{$tenantId}/" . $request->file('file')->getClientOriginalName();

        Storage::put($filePath, file_get_contents($request->file('file')->getRealPath()));

        return response()->json(['path' => Storage::url($filePath)]);
    }
}
```

## Geração de URLs Temporárias para Tenants

Você pode gerar URLs temporárias específicas para arquivos de um tenant.

### Exemplo de Geração de URL Temporária

```php
$tenantId = app('tenant');
$url = Storage::temporaryUrl(
    "tenants/{$tenantId}/file.txt", now()->addMinutes(30)
);

echo $url;
```

## Benefícios do Multitenancy

- **Isolamento de Dados**: Garante que os dados de cada tenant estejam isolados e seguros.
- **Escalabilidade**: Facilita a gestão e escalabilidade da aplicação para múltiplos clientes.
- **Personalização**: Permite personalizar a experiência de cada tenant, atendendo às suas necessidades específicas.

## Resumo

Implementar um cenário de multitenancy no Laravel para armazenamento de arquivos garante que cada tenant tenha seu próprio espaço de armazenamento isolado e seguro. Utilizando abordagens como diretórios separados ou prefixos de tenant, você pode organizar e gerenciar os arquivos de forma eficiente, proporcionando uma experiência personalizada e segura para cada cliente.
