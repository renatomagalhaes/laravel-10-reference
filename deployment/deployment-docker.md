# Deploy com Docker

Docker é uma plataforma que facilita a criação, implantação e execução de aplicações em contêineres. Utilizar Docker para deploy garante consistência entre os ambientes de desenvolvimento, teste e produção, reduzindo problemas relacionados à compatibilidade e configuração.

## Configurando Docker para Deploy

### Criação do Dockerfile

Um Dockerfile é um script que contém instruções para construir uma imagem Docker. Crie um Dockerfile na raiz do seu projeto Laravel com o seguinte conteúdo:

```Dockerfile
# Escolhe a imagem base
FROM php:8.0-fpm

# Instala dependências do sistema
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql

# Instala Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www

# Copia os arquivos do projeto
COPY . .

# Instala dependências do Composer
RUN composer install --no-dev --optimize-autoloader

# Define o comando padrão
CMD ["php-fpm"]
```

### Configuração do Docker Compose

Docker Compose é uma ferramenta para definir e executar aplicações multi-contêiner. Crie um arquivo `docker-compose.yml` para configurar os serviços necessários para a aplicação Laravel.

#### Exemplo de Arquivo docker-compose.yml

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
    volumes:
      - .:/var/www
    working_dir: /var/www
    environment:
      - APP_ENV=production
      - DB_HOST=db
      - DB_DATABASE=laravel
      - DB_USERNAME=root
      - DB_PASSWORD=root
    depends_on:
      - db
    ports:
      - "9000:9000"
    networks:
      - laravel

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=laravel
    ports:
      - "3306:3306"
    networks:
      - laravel

  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "80:80"
    depends_on:
      - app
    networks:
      - laravel

networks:
  laravel:
    driver: bridge
```

### Configuração do Nginx

Crie um arquivo `nginx.conf` para configurar o Nginx como servidor web.

#### Exemplo de Arquivo nginx.conf

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## Deploy de Contêineres Docker

### Construir e Iniciar os Contêineres

1. **Construir as Imagens:**
   ```bash
   docker-compose build
   ```

2. **Iniciar os Contêineres:**
   ```bash
   docker-compose up -d
   ```

### Verificação do Deploy

1. **Acessar a Aplicação:**
   - Abra o navegador e acesse `http://localhost` para verificar se a aplicação está funcionando.

2. **Verificar Logs:**
   - Use o comando `docker-compose logs` para visualizar os logs dos contêineres e identificar possíveis problemas.

## Práticas Recomendadas

### Uso de Variáveis de Ambiente

Armazene configurações sensíveis (como credenciais de banco de dados) em variáveis de ambiente para garantir a segurança e facilitar a gestão de configurações.

### Otimização de Imagens Docker

Minimize o tamanho das imagens Docker removendo arquivos temporários e desnecessários após a instalação de dependências. Utilize a instrução `--no-cache` durante a construção da imagem para garantir que as dependências sejam sempre atualizadas.

### Automação com CI/CD

Automatize o processo de construção e implantação de contêineres Docker utilizando ferramentas de CI/CD como GitLab CI/CD, Jenkins ou GitHub Actions. Configure pipelines para construir, testar e implantar imagens Docker automaticamente.

### Monitoramento de Contêineres

Implemente soluções de monitoramento para acompanhar a saúde e o desempenho dos contêineres em produção. Ferramentas como Prometheus, Grafana e ELK Stack (Elasticsearch, Logstash, Kibana) podem ser usadas para monitoramento e análise de logs.

## Exemplo Completo

### Dockerfile Completo

```Dockerfile
# Escolhe a imagem base
FROM php:8.0-fpm

# Instala dependências do sistema
RUN apt-get update && apt-get install -y \
    git \
    unzip \
    libpng-dev \
    libjpeg-dev \
    libfreetype6-dev \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install gd pdo pdo_mysql

# Instala Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Define o diretório de trabalho
WORKDIR /var/www

# Copia os arquivos do projeto
COPY . .

# Instala dependências do Composer
RUN composer install --no-dev --optimize-autoloader

# Define o comando padrão
CMD ["php-fpm"]
```

### docker-compose.yml Completo

```yaml
version: '3.8'

services:
  app:
    build:
      context: .
    volumes:
      - .:/var/www
    working_dir: /var/www
    environment:
      - APP_ENV=production
      - DB_HOST=db
      - DB_DATABASE=laravel
      - DB_USERNAME=root
      - DB_PASSWORD=root
    depends_on:
      - db
    ports:
      - "9000:9000"
    networks:
      - laravel

  db:
    image: mysql:5.7
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=laravel
    ports:
      - "3306:3306"
    networks:
      - laravel

  nginx:
    image: nginx:alpine
    volumes:
      - ./nginx.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "80:80"
    depends_on:
      - app
    networks:
      - laravel

networks:
  laravel:
    driver: bridge
```

### nginx.conf Completo

```nginx
server {
    listen 80;
    server_name localhost;

    root /var/www/public;
    index index.php index.html index.htm;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include fastcgi_params;
        fastcgi_pass app:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

## Resumo

Utilizar Docker para deploy garante que a aplicação seja implantada em um ambiente consistente e isolado. Com Docker e Docker Compose, você pode configurar e gerenciar facilmente contêineres para sua aplicação Laravel, garantindo eficiência e confiabilidade no processo de deploy.
