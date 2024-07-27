# Deploy com Laravel Forge

Laravel Forge é uma ferramenta de gerenciamento de servidores que facilita a configuração e o gerenciamento de servidores para hospedar aplicações PHP. Ele oferece uma interface simples para automação de tarefas comuns, como configuração de servidores, implantação de código e gerenciamento de SSL.

## Configurando Laravel Forge

### Criação de uma Conta

1. **Registrar no Laravel Forge:**
   - Acesse [Laravel Forge](https://forge.laravel.com) e crie uma conta.
   - Complete o processo de registro e verificação.

2. **Adicionar uma Chave SSH:**
   - Gere uma chave SSH no seu computador (se ainda não tiver uma).
   ```bash
   ssh-keygen -t rsa -b 4096 -C "seu-email@example.com"
   ```
   - Adicione a chave pública ao Laravel Forge.

3. **Conectar à DigitalOcean:**
   - Acesse "Server Providers" no Laravel Forge.
   - Clique em "Connect" em DigitalOcean (ou outro provedor de sua escolha).
   - Siga as instruções para conectar a sua conta.

### Criação de um Novo Servidor

1. **Criar Servidor:**
   - No Laravel Forge, clique em "Create Server".
   - Selecione o provedor (por exemplo, DigitalOcean).
   - Configure os detalhes do servidor, como localização, tamanho e nome.
   - Clique em "Create Server" para iniciar a criação.

2. **Configuração do Servidor:**
   - Após a criação, Laravel Forge configurará automaticamente o servidor com os requisitos necessários (Nginx, PHP, MySQL, etc.).
   - Receba notificações sobre a conclusão da configuração.

## Processos de Deploy com Laravel Forge

### Configuração do Site

1. **Adicionar um Site:**
   - No painel do Laravel Forge, selecione o servidor.
   - Clique em "Sites" e depois em "Create Site".
   - Preencha os detalhes do site, como domínio e diretório raiz.
   - Clique em "Create Site" para adicionar o site ao servidor.

2. **Configuração de Domínio:**
   - Configure o domínio DNS para apontar para o endereço IP do servidor.
   - No painel do Laravel Forge, selecione o site e clique em "Meta" para verificar o status do domínio.

### Configuração do Deploy

1. **Adicionar Repositório Git:**
   - No painel do Laravel Forge, selecione o site.
   - Clique em "Deploy" e depois em "Git Repository".
   - Adicione o URL do repositório Git e configure a branch de deploy.
   - Clique em "Update Repository".

2. **Configuração do Script de Deploy:**
   - No painel do Laravel Forge, clique em "Deploy Script".
   - Configure o script de deploy com as etapas necessárias, como instalação de dependências, migração de banco de dados, etc.
   ```bash
   cd /home/forge/seu-dominio.com
   git pull origin main
   composer install --no-interaction --prefer-dist --optimize-autoloader
   php artisan migrate --force
   ```
   - Salve o script de deploy.

3. **Deploy Automático:**
   - No painel do Laravel Forge, ative a opção "Deploy on Push" para implantações automáticas sempre que houver um push na branch configurada.

### Gerenciamento de SSL

1. **Adicionar Certificado SSL:**
   - No painel do Laravel Forge, selecione o site.
   - Clique em "SSL" e depois em "Let's Encrypt".
   - Preencha os detalhes necessários e clique em "Obtain Certificate" para gerar e instalar um certificado SSL gratuito.

2. **Renovação Automática:**
   - Laravel Forge configura automaticamente a renovação dos certificados SSL emitidos pelo Let's Encrypt.

### Monitoramento e Backups

1. **Configuração de Monitoramento:**
   - Laravel Forge integra-se com ferramentas de monitoramento como Laravel Horizon, New Relic, etc.
   - Configure alertas para monitorar a saúde do servidor e da aplicação.

2. **Configuração de Backups:**
   - Laravel Forge permite configurar backups automáticos do banco de dados.
   - No painel do Laravel Forge, selecione o servidor e clique em "Database".
   - Configure os detalhes do backup, incluindo a frequência e o destino (por exemplo, Amazon S3).

## Resumo

Laravel Forge simplifica o processo de configuração e gerenciamento de servidores para hospedar aplicações Laravel. Com funcionalidades como deploy automatizado, gerenciamento de SSL e monitoramento integrado, Laravel Forge facilita a implantação e manutenção de aplicações em produção, garantindo eficiência e confiabilidade.
