# Rollback em Produção

Ter um plano de rollback é essencial para garantir que você possa reverter rapidamente qualquer mudança que cause problemas na aplicação. Este guia cobre as práticas recomendadas e ferramentas para realizar rollbacks eficazes em um ambiente de produção.

## Estratégias de Rollback

### Backup Pré-Deploy

1. **Criação de Backup Antes do Deploy:**
   - Antes de realizar qualquer deploy, crie um backup completo do banco de dados e dos arquivos críticos.

#### Exemplo de Backup de Banco de Dados

```bash
mysqldump -u root -p database_name > backup.sql
```

#### Exemplo de Backup de Arquivos

```bash
tar -czvf backup.tar.gz /path/to/important/files
```

### Deploy Atômico

1. **Utilização de Links Simbólicos:**
   - Implante o novo código em um diretório separado e use links simbólicos para alternar entre as versões.

#### Exemplo de Deploy Atômico

```bash
# Define variáveis
RELEASE_DIR="/var/www/releases"
CURRENT_DIR="/var/www/current"
NEW_RELEASE_DIR="$RELEASE_DIR/$(date +%Y%m%d%H%M%S)"

# Cria o novo diretório de release
mkdir -p $NEW_RELEASE_DIR

# Copia os arquivos para o novo diretório de release
cp -R /path/to/new/code/* $NEW_RELEASE_DIR

# Atualiza o link simbólico
ln -sfn $NEW_RELEASE_DIR $CURRENT_DIR

# Reinicia o servidor web
systemctl restart nginx
```

### Blue-Green Deploy

1. **Ambientes Blue e Green:**
   - Mantenha dois ambientes idênticos e alterna entre eles para minimizar downtime e facilitar rollbacks.

#### Exemplo de Alternância de Ambientes

```bash
# Define variáveis
ACTIVE_ENV="blue"
INACTIVE_ENV="green"

# Atualiza o ambiente inativo
deploy_to_environment $INACTIVE_ENV

# Redireciona o tráfego para o ambiente inativo, tornando-o ativo
switch_traffic $INACTIVE_ENV

# Atualiza as variáveis de ambiente
TEMP=$ACTIVE_ENV
ACTIVE_ENV=$INACTIVE_ENV
INACTIVE_ENV=$TEMP
```

## Execução de Rollback

### Rollback de Código

1. **Reverter para um Commit Anterior:**
   - Utilize o Git para reverter o código para um commit anterior.

#### Exemplo de Rollback com Git

```bash
git checkout <commit_id>
git push origin main --force
```

### Rollback de Banco de Dados

1. **Restauração de Backup:**
   - Restaure o banco de dados a partir de um backup.

#### Exemplo de Restauração de Banco de Dados

```bash
mysql -u root -p database_name < backup.sql
```

### Automação de Rollbacks

1. **Scripts de Rollback:**
   - Crie scripts automatizados para realizar rollbacks rapidamente.

#### Exemplo de Script de Rollback

```bash
#!/bin/bash

# Define variáveis
BACKUP_FILE="/path/to/backup.sql"
DB_NAME="database_name"
DB_USER="root"
DB_PASS="password"

# Restaura o banco de dados
mysql -u $DB_USER -p$DB_PASS $DB_NAME < $BACKUP_FILE

# Reverte o código para um commit anterior
git checkout <commit_id>
git push origin main --force

# Reinicia o servidor web
systemctl restart nginx
```

## Monitoramento e Validação

### Monitoramento Pós-Rollback

1. **Verificação de Logs:**
   - Monitore os logs da aplicação e do servidor para garantir que o rollback foi bem-sucedido.

```bash
tail -f /var/log/nginx/error.log
tail -f /var/www/html/storage/logs/laravel.log
```

2. **Testes Automatizados:**
   - Execute testes automatizados para validar que a aplicação está funcionando corretamente após o rollback.

```bash
php artisan test
```

## Práticas Recomendadas

### Planejamento de Rollback

1. **Plano de Rollback Detalhado:**
   - Documente um plano de rollback detalhado e treine sua equipe para executar o rollback rapidamente.

### Backup Regular

1. **Automatização de Backups:**
   - Automatize o processo de backup para garantir que você tenha sempre uma cópia recente dos dados.

### Verificação Regular

1. **Testes de Rollback:**
   - Realize testes regulares de rollback para garantir que o processo funcione conforme esperado.

## Resumo

Ter um plano de rollback eficaz é crucial para garantir que você possa rapidamente reverter qualquer mudança que cause problemas na aplicação. Utilizando práticas recomendadas e ferramentas automatizadas, você pode minimizar o downtime e garantir a estabilidade da aplicação em produção.
