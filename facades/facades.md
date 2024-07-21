# Facades

Facades no Laravel fornecem uma interface estática para classes no container de serviços do Laravel. Elas permitem o uso de métodos estáticos para acessar várias funcionalidades do framework de maneira simples e expressiva.

## Índice

1. [Create](facades-create.md)
2. [Read](facades-read.md)
3. [Update](facades-update.md)
4. [Delete](facades-delete.md)
5. [Statements](facades-statements.md)
6. [Transações - Commit e Rollback](facades-transactions.md)
7. [Cenário Multitenant](facades-multitenant.md)
8. [Banco de Leitura e Escrita com CQRS](facades-cqrs.md)
9. [Custom Facades](facades-custom.md)
10. [Facades e Dependency Injection](facades-dependency-injection.md)
11. [Facades e Testes Unitários](facades-testing.md)
12. [Facades e Middleware](facades-middleware.md)
13. [Facades e Segurança](facades-security.md)

## Resumo

### Create

Facades podem ser usadas para realizar operações CRUD, incluindo a criação de registros. Utilizar facades para criar registros no banco de dados é uma maneira eficiente e expressiva de interagir com o Laravel.

### Read

Facades podem ser usadas para consultar dados do banco de dados. Elas simplificam o acesso a várias funcionalidades do framework, facilitando a realização de operações de leitura.

### Update

Facades podem ser usadas para atualizar registros existentes no banco de dados. Utilizar facades para operações de atualização torna o código mais expressivo e fácil de entender.

### Delete

Facades podem ser usadas para excluir registros do banco de dados. Elas simplificam o acesso a várias funcionalidades do framework, e o suporte a soft deletes adiciona flexibilidade na gestão de dados deletados.

### Statements

Facades podem ser usadas para executar declarações SQL brutas diretamente no banco de dados, permitindo realizar operações que não são suportadas diretamente pelo Eloquent ou Query Builder.

### `facades.md` (continuação)

```markdown
### Transações - Commit e Rollback

Facades fornecem uma maneira prática de gerenciar transações no banco de dados, garantindo a integridade dos dados em casos de falhas ou erros durante a execução de operações.

### Cenário Multitenant

Facades podem ser usadas eficazmente para implementar multitenancy, garantindo o isolamento adequado dos dados de cada cliente. Utilizar middleware ou escopos globais pode ajudar a garantir que cada consulta esteja filtrada pelo tenant atual.

### Banco de Leitura e Escrita com CQRS

O padrão CQRS ajuda a otimizar operações de leitura e escrita separando a lógica em diferentes modelos ou bancos de dados. Utilizar facades para gerenciar CQRS facilita a implementação de operações seguras e eficientes, melhorando a performance e escalabilidade da aplicação.

### Custom Facades

Facades personalizadas são uma ferramenta poderosa para encapsular lógica complexa e tornar sua aplicação mais modular e organizada. Criar e utilizar facades personalizadas em sua aplicação Laravel pode melhorar a estrutura e reutilização de código.

### Facades e Dependency Injection

Combinar facades com injeção de dependência permite criar código flexível e testável, mantendo a simplicidade e organização proporcionadas pelas facades. Utilize a injeção de dependência para serviços que requerem testabilidade e flexibilidade, e facades para simplificar o acesso a esses serviços.

### Facades e Testes Unitários

Testar facades utilizando mocks e espiões garante que seu código funcione corretamente e que as interações com os serviços subjacentes sejam simuladas de maneira precisa. Utilize esses métodos para criar testes robustos e confiáveis.

### Facades e Middleware

Utilizar facades em middleware permite centralizar a lógica transversal da aplicação, tornando o código mais organizado e facilitando a manutenção e atualização das regras de negócio.

### Facades e Segurança

Implementar segurança utilizando facades ajuda a centralizar e padronizar as práticas de segurança na aplicação. Facades tornam o código mais organizado e facilitam a aplicação de políticas de segurança em toda a aplicação.
