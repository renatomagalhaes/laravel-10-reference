# CouchDB

CouchDB é um banco de dados NoSQL que usa o formato JSON para armazenar dados e é acessado via HTTP. Podemos integrar CouchDB ao Laravel usando pacotes como `doctrine/couchdb-odm`.

## Instalação

Para instalar o pacote `doctrine/couchdb-odm`, use o Composer:

```bash
composer require doctrine/couchdb-odm
```

## Configuração

Para configurar o CouchDB, adicione a configuração da conexão no arquivo `config/database.php`:

```php
'couchdb' => [
    'driver' => 'couchdb',
    'host' => env('DB_HOST', '127.0.0.1'),
    'port' => env('DB_PORT', 5984),
    'database' => env('DB_DATABASE', 'homestead'),
    'username' => env('DB_USERNAME', 'homestead'),
    'password' => env('DB_PASSWORD', 'secret'),
],
```

## Definindo Documentos

No CouchDB, você define documentos em vez de modelos.

### Exemplo de Documento

```php
use Doctrine\ODM\CouchDB\Mapping\Annotations as ODM;

/**
 * @ODM\Document
 */
class User
{
    /** @ODM\Id */
    private $id;

    /** @ODM\Field(type="string") */
    private $name;

    /** @ODM\Field(type="string") */
    private $email;

    // Métodos getter e setter
}
```

## Consultas Simples

Você pode usar o CouchDB ODM para fazer consultas aos documentos.

### Exemplo de Consulta

```php
$userRepository = $dm->getRepository(User::class);
$users = $userRepository->findAll();
$user = $userRepository->find('60b8d2950c7e4b24d8f5ad10');
$activeUsers = $userRepository->findBy(['active' => true]);
```
