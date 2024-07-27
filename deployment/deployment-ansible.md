# Deploy com Ansible

Ansible é uma ferramenta de automação de TI que facilita a configuração de sistemas, a implantação de software e a execução de tarefas de TI de maneira repetível e escalável. Utilizando Ansible para deploy, você pode garantir consistência e eficiência no processo de implantação da sua aplicação Laravel.

## Configurando Ansible para Deploy

### Instalação do Ansible

1. **Instalação do Ansible:**
   - Instale o Ansible em sua máquina de controle (o computador a partir do qual você executará os comandos Ansible).

```bash
sudo apt-get update
sudo apt-get install ansible
```

### Criação de Playbooks Ansible

1. **Playbook para Configuração de Servidor:**
   - Crie um playbook Ansible para configurar o servidor para rodar a aplicação Laravel.

#### Exemplo de Playbook para Configuração de Servidor

```yaml
- hosts: webservers
  become: yes
  tasks:
    - name: Atualizar o sistema
      apt:
        update_cache: yes

    - name: Instalar dependências
      apt:
        name:
          - nginx
          - php-fpm
          - php-mysql
          - git
        state: present

    - name: Configurar Nginx
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify:
        - restart nginx

    - name: Clonar repositório do Git
      git:
        repo: 'https://github.com/seu-usuario/seu-repositorio.git'
        dest: /var/www/html
        version: main

    - name: Instalar dependências do Composer
      command: composer install --no-dev --optimize-autoloader chdir=/var/www/html

    - name: Configurar permissões
      file:
        path: /var/www/html
        owner: www-data
        group: www-data
        recurse: yes

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

### Criação de Arquivos de Template

1. **Arquivo de Configuração do Nginx (nginx.conf.j2):**
   - Crie um arquivo de template para configurar o Nginx.

```nginx
server {
    listen 80;
    server_name seu-dominio.com;

    root /var/www/html/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

### Execução do Playbook

1. **Definição do Arquivo de Inventário:**
   - Crie um arquivo de inventário que liste os servidores onde o playbook será executado.

#### Exemplo de Arquivo de Inventário (hosts)

```ini
[webservers]
server1 ansible_host=192.168.1.100 ansible_user=deploy
server2 ansible_host=192.168.1.101 ansible_user=deploy
```

2. **Execução do Playbook:**
   - Execute o playbook Ansible para configurar os servidores e implantar a aplicação Laravel.

```bash
ansible-playbook -i hosts playbook.yml
```

## Playbooks de Ansible para Laravel

### Playbook para Deploy Contínuo

1. **Playbook de Deploy:**
   - Crie um playbook para automatizar o processo de deploy contínuo.

#### Exemplo de Playbook de Deploy

```yaml
- hosts: webservers
  become: yes
  tasks:
    - name: Clonar repositório do Git
      git:
        repo: 'https://github.com/seu-usuario/seu-repositorio.git'
        dest: /var/www/html
        version: main

    - name: Instalar dependências do Composer
      command: composer install --no-dev --optimize-autoloader chdir=/var/www/html

    - name: Executar migrações do banco de dados
      command: php artisan migrate --force chdir=/var/www/html

    - name: Reiniciar Nginx
      service:
        name: nginx
        state: restarted
```

## Práticas Recomendadas

### Automação de Tarefas Comuns

1. **Automatização de Tarefas:**
   - Automatize tarefas comuns como configuração de servidores, instalação de dependências e execução de migrações para reduzir erros manuais e aumentar a eficiência.

### Gestão de Inventário

1. **Organização do Inventário:**
   - Mantenha um arquivo de inventário organizado e atualizado para facilitar a gestão dos servidores.

### Monitoramento e Notificações

1. **Configuração de Notificações:**
   - Configure notificações para monitorar o status das execuções dos playbooks e detectar possíveis falhas.

## Exemplo Completo

### Playbook Completo

```yaml
- hosts: webservers
  become: yes
  tasks:
    - name: Atualizar o sistema
      apt:
        update_cache: yes

    - name: Instalar dependências
      apt:
        name:
          - nginx
          - php-fpm
          - php-mysql
          - git
        state: present

    - name: Configurar Nginx
      template:
        src: templates/nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify:
        - restart nginx

    - name: Clonar repositório do Git
      git:
        repo: 'https://github.com/seu-usuario/seu-repositorio.git'
        dest: /var/www/html
        version: main

    - name: Instalar dependências do Composer
      command: composer install --no-dev --optimize-autoloader chdir=/var/www/html

    - name: Executar migrações do banco de dados
      command: php artisan migrate --force chdir=/var/www/html

    - name: Configurar permissões
      file:
        path: /var/www/html
        owner: www-data
        group: www-data
        recurse: yes

  handlers:
    - name: restart nginx
      service:
        name: nginx
        state: restarted
```

## Resumo

Ansible facilita a automação do processo de deploy, garantindo consistência e eficiência. Com playbooks bem definidos, você pode configurar servidores, instalar dependências e implantar sua aplicação Laravel de maneira repetível e escalável.
