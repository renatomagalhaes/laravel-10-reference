# Banco de Dados no Laravel 10

Este tópico cobre os principais aspectos do trabalho com bancos de dados no Laravel. A seguir, apresentamos um resumo detalhado e um índice dos subtópicos abordados.

## Resumo

O Laravel oferece suporte robusto para trabalhar com bancos de dados através do Eloquent ORM e do Query Builder. Ele facilita a configuração de conexões com múltiplos bancos de dados, a criação e gestão de migrations, seeders, factories, e consultas eficientes usando joins e relações. Além disso, o Laravel permite a implementação de cenários avançados como multitenancy e replicação mestre-escravo.

### Tipos de Bancos de Dados Suportados

O Laravel suporta vários tipos de bancos de dados relacionais e NoSQL, incluindo:

- **Relacionais:**
  - MySQL
  - PostgreSQL
  - SQLite
  - SQL Server
- **NoSQL:**
  - MongoDB
  - CouchDB
  - DynamoDB

### Configurações e Práticas Recomendadas

- **Configuração de Banco de Dados:** O Laravel facilita a configuração de múltiplas conexões de banco de dados no arquivo `.env`.
- **Migrations:** Permitem a criação e modificação de tabelas de forma versionada e controlada.
- **Seeders e Factories:** Usados para popular o banco de dados com dados de teste e gerar modelos de dados de forma conveniente.
- **Query Builder:** Uma interface fluente para construir consultas SQL.
- **Eloquent ORM:** Uma ferramenta poderosa para mapeamento objeto-relacional.

## Índice

1. [Configuração do Banco de Dados](database-configuration.md)
2. [Migrations](database-migrations.md)
3. [Seeders](database-seeders.md)
4. [Factories](database-factories.md)
5. [Query Builder](database-query-builder.md)
6. [Conexão com Múltiplos Bancos de Dados](database-multiple-connections.md)
7. [Cenário Multitenant](database-multitenancy.md)
8. [Cenário de Banco Master e Réplica](database-master-replica.md)
9. [Consultas Otimizadas com Inner Joins](database-inner-joins.md)
10. [MongoDB](database-mongodb.md)
11. [CouchDB](database-couchdb.md)
12. [DynamoDB](database-dynamodb.md)
