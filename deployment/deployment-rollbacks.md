# Rollbacks e Desfazer Deploys

Rollbacks são processos essenciais que permitem reverter uma aplicação para um estado anterior em caso de falhas ou problemas após um deploy. Ter um plano de rollback bem definido garante que a aplicação possa ser rapidamente restaurada, minimizando o downtime e os impactos negativos para os usuários.

## Estratégias de Rollback

### Backup de Banco de Dados

Antes de qualquer deploy, é crucial fazer um backup do banco de dados. Isso garante que, em caso de falhas, os dados possam ser restaurados para um estado anterior.

#### Exemplo de Backup de Banco de Dados

```bash
mysqldump -u root -p database_name > backup.sql
```

### Controle de Versão

Utilizar um sistema de controle de versão como Git permite reverter o código para uma versão anterior com facilidade.

#### Exemplo de Rollback com Git

```bash
git checkout <commit_id>
git push origin main --force
```

### Deploy Atômico

Implementar deploys atômicos, onde o código é implantado em um novo diretório e um link simbólico é atualizado para apontar para a nova versão, facilita a reversão para uma versão anterior.

#### Exemplo de Deploy Atômico

```bash
ln -sfn /path/to/new_release /path/to/current_release
```

### Ferramentas de Rollback

Ferramentas como Laravel Envoyer, Forge e outros serviços de deploy contínuo oferecem funcionalidades de rollback integradas, facilitando a reversão de deploys com apenas alguns cliques.

## Automação de Rollbacks

### Scripts de Rollback

Automatizar o processo de rollback com scripts garante que a reversão seja feita de maneira rápida e consistente.

#### Exemplo de Script de Rollback

Crie um script `rollback.sh`:

```bash
#!/bin/bash

# Define a versão para a qual deseja reverter
VERSION=$1

# Caminho do diretório de releases
RELEASE_DIR="/path/to/releases"

# Atualiza o link simbólico para apontar para a versão anterior
ln -sfn $RELEASE_DIR/$VERSION /path/to/current_release

# Reinicia o servidor web
systemctl restart nginx
```

### Integração com CI/CD

Integre scripts de rollback com pipelines de CI/CD para permitir reversões automáticas em caso de falhas nos testes de integração.

#### Exemplo de Integração com GitLab CI/CD

Adicione um job de rollback no arquivo `.gitlab-ci.yml`:

```yaml
stages:
  - test
  - deploy
  - rollback

rollback:
  stage: rollback
  script:
    - ./rollback.sh <version>
  only:
    - main
  when: on_failure
```

### Monitoramento e Alertas

Implementar monitoramento e alertas para detectar problemas rapidamente e acionar rollbacks automáticos quando necessário.

#### Exemplo de Configuração de Alertas

Configure alertas no Laravel Envoyer para monitorar a saúde da aplicação e acionar rollbacks automáticos em caso de falhas.

## Resumo

Ter um plano de rollback bem definido é essencial para garantir a estabilidade e a disponibilidade da aplicação em produção. Estratégias como backups de banco de dados, controle de versão, deploy atômico e automação de rollbacks garantem que a aplicação possa ser rapidamente restaurada em caso de falhas, minimizando o downtime e os impactos negativos para os usuários.
