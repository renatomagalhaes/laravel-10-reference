# Deploy com Laravel Envoyer

Laravel Envoyer é uma ferramenta de deploy zero-downtime que facilita a implantação contínua de aplicações PHP. Ele oferece uma interface intuitiva para gerenciar o deploy de código, monitorar a saúde das aplicações e gerenciar releases.

## Configurando Laravel Envoyer

### Criação de uma Conta

1. **Registrar no Laravel Envoyer:**
   - Acesse [Laravel Envoyer](https://envoyer.io) e crie uma conta.
   - Complete o processo de registro e verificação.

### Criação de um Novo Projeto

1. **Adicionar um Projeto:**
   - No painel do Laravel Envoyer, clique em "Projects" e depois em "Create Project".
   - Preencha os detalhes do projeto, como nome e repositório Git.
   - Selecione o tipo de projeto (por exemplo, Laravel).
   - Clique em "Create Project" para adicionar o projeto.

### Configuração de Servidores

1. **Adicionar um Servidor:**
   - No painel do Laravel Envoyer, selecione o projeto.
   - Clique em "Servers" e depois em "Add Server".
   - Preencha os detalhes do servidor, como endereço IP, usuário SSH e caminho do projeto.
   - Clique em "Add Server" para adicionar o servidor ao projeto.

2. **Configuração de Chave SSH:**
   - Laravel Envoyer gerará uma chave SSH para o servidor.
   - Adicione a chave pública SSH ao servidor remoto para permitir a conexão.

## Gerenciamento de Releases com Envoyer

### Configuração de Deploy

1. **Adicionar Repositório Git:**
   - No painel do Laravel Envoyer, selecione o projeto.
   - Clique em "Repository" e configure o repositório Git.
   - Adicione o URL do repositório e configure a branch de deploy.
   - Clique em "Update Repository".

2. **Configuração do Script de Deploy:**
   - No painel do Laravel Envoyer, clique em "Deploy Script".
   - Configure o script de deploy com as etapas necessárias, como instalação de dependências, migração de banco de dados, etc.
   ```bash
   cd {{ release }}
   git pull origin main
   composer install --no-interaction --prefer-dist --optimize-autoloader
   php artisan migrate --force
   ```
   - Salve o script de deploy.

3. **Deploy Automático:**
   - No painel do Laravel Envoyer, ative a opção "Deploy on Push" para implantações automáticas sempre que houver um push na branch configurada.

### Gerenciamento de Rollbacks

1. **Executar Rollback:**
   - No painel do Laravel Envoyer, selecione o projeto.
   - Clique em "Releases" para ver todas as releases implantadas.
   - Selecione a release para a qual deseja reverter e clique em "Rollback".

### Monitoramento e Alertas

1. **Configuração de Health Checks:**
   - Laravel Envoyer permite configurar verificações de saúde para monitorar a disponibilidade e a performance da aplicação.
   - No painel do Laravel Envoyer, selecione o projeto.
   - Clique em "Health Checks" e configure as URLs a serem monitoradas.

2. **Configuração de Alertas:**
   - Configure alertas para receber notificações em caso de problemas com a aplicação.
   - No painel do Laravel Envoyer, selecione o projeto.
   - Clique em "Notifications" e configure os canais de notificação, como Slack ou e-mail.

## Deploy Zero-Downtime

### Estratégia de Deploy Zero-Downtime

1. **Deploy Atômico:**
   - Laravel Envoyer utiliza uma estratégia de deploy atômico, onde uma nova release é criada em um diretório separado e, após a conclusão do deploy, um link simbólico é atualizado para apontar para a nova release.

2. **Sincronização de Arquivos:**
   - Durante o deploy, arquivos e diretórios críticos (como `storage` e `vendor`) são sincronizados para garantir consistência e integridade.

### Testes de Release

1. **Testar Nova Release:**
   - Após o deploy, teste a nova release para garantir que a aplicação está funcionando corretamente.
   - No painel do Laravel Envoyer, clique em "Releases" e selecione a release recém-implantada.
   - Execute verificações manuais ou automatizadas para confirmar a integridade da release.

## Resumo

Laravel Envoyer facilita o deploy zero-downtime de aplicações Laravel, oferecendo funcionalidades como deploy atômico, gerenciamento de releases, monitoramento de saúde e alertas. Com Envoyer, você pode garantir que suas aplicações sejam implantadas de maneira eficiente e confiável, minimizando o downtime e melhorando a experiência do usuário.
