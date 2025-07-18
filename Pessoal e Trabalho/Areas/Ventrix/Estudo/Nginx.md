#estudo #ventrix 

### **NginX**

NginX é um servidor web de alto desempenho e leve, projetado para lidar com muitos acessos simultâneos.

- **Usos comuns:**
    - Servir arquivos estáticos (HTML, CSS, JS).
    - Atuar como _proxy reverso_ para aplicações backend (ex: Node, Django).
    - Fazer _load balancing_ (distribuir requisições entre múltiplas instâncias).
- **Vantagens:** baixo consumo de memória, alta velocidade e suporte a configuração modular.

### **1. Como começar com NginX**

**Passos iniciais:**

1. Instale o NginX:
    
    - No Ubuntu: `sudo apt install nginx`
        
    - Ou use via Docker:
        
        bash
        `docker run -d -p 8080:80 nginx`
        
2. Acesse `http://localhost:8080` para ver a página padrão.
    
3. Estude os arquivos de configuração:
    
    - Local padrão: `/etc/nginx/nginx.conf` ou `/etc/nginx/sites-available/default`
        
4. Aprenda a criar um _reverse proxy_ para uma aplicação:
    
    nginx
    `location / {   proxy_pass http://localhost:3000; }`
    
5. Documentação: [NginX Docs](nginx.org/en/docs)


## ✅ **Cenário Recomendado: Load Balancer + NGINX como Reverse Proxy**

### **Arquitetura:**

rust

CopiarEditar

`Usuário --> Load Balancer (AWS ELB) --> EC2 (Docker + NGINX) --> Container (App/API)`

---

## 🧩 1. **Escolha a função do NGINX:**

Nginx pode servir como:

- 🔁 **Reverse Proxy** (encaminhar requisições para apps/backend)
    
- 🌐 **Servidor Web** (servir arquivos estáticos ou apps frontend)
    
- 🔐 **HTTPS Termination** (SSL/TLS)
    
- ⚖️ **Load Balancer interno** (se não usar ELB externo da AWS)
    

---

## 🏗️ 2. **Infraestrutura recomendada na AWS**

|Componente|Função|
|---|---|
|**EC2**|Hospeda seu serviço (pode ser com Docker)|
|**Elastic Load Balancer (ELB)**|Distribui tráfego entre instâncias EC2|
|**Auto Scaling Group**|Escala horizontalmente os EC2 (se necessário)|
|**Route 53 (opcional)**|DNS personalizado (ex: `api.seusite.com`)|
|**Certbot/ACM**|Gerencia certificados SSL/TLS|
|**EFS ou S3**|Armazenamento compartilhado (opcional para arquivos estáticos)|

---

## ⚙️ 3. **Configuração típica do NGINX (reverse proxy + HTTPS):**

nginx

CopiarEditar

`server {     listen 80;     server_name api.seusite.com;      # Redireciona para HTTPS     return 301 https://$host$request_uri; }  server {     listen 443 ssl;     server_name api.seusite.com;      ssl_certificate     /etc/letsencrypt/live/api.seusite.com/fullchain.pem;     ssl_certificate_key /etc/letsencrypt/live/api.seusite.com/privkey.pem;      location / {         proxy_pass http://localhost:3000; # ou outro container         proxy_http_version 1.1;         proxy_set_header Host $host;         proxy_set_header X-Real-IP $remote_addr;     } }`

---

## 🐳 4. **Docker + Docker Compose (opcional)**

Você pode rodar seu app e o Nginx em containers:

**docker-compose.yml:**

yaml

CopiarEditar

`services:   nginx:     image: nginx:latest     ports:       - "80:80"       - "443:443"     volumes:       - ./nginx.conf:/etc/nginx/conf.d/default.conf       - ./certs:/etc/letsencrypt     depends_on:       - app    app:     image: sua-imagem-api     expose:       - "3000"`

---

## 🛡️ 5. **Segurança**

- Use **Security Groups** para restringir acesso (ex: só porta 443 exposta).
    
- Utilize **ACM** ou **Let's Encrypt** com **Certbot** para HTTPS.
    
- Considere usar **WAF (Web Application Firewall)** para proteger endpoints.
    

---

## 🚀 6. **Deploy e Escalabilidade**

- Para apps de produção, considere:
    
    - Colocar o NGINX atrás de um **ELB (Elastic Load Balancer)**.
        
    - Criar um **Auto Scaling Group** com AMIs contendo NGINX + app configurados.
        
    - Usar **Docker** ou **ECS (com Fargate)** se quiser abstrair EC2.
        

---

## 🔧 Alternativas modernas:

Se você quer menos manutenção:

- ✅ **AWS Elastic Beanstalk + NGINX** (muito usado para apps Node/Python)
    
- ✅ **AWS ECS com ALB + NGINX dentro do container**
    
- ✅ **AWS CloudFront + S3 + NGINX para frontend estático**
    

---

Se você quiser, posso te ajudar a montar um `docker-compose.yml` com NGINX + app + HTTPS, ou uma infraestrutura AWS com EC2 + Load Balancer. É só dizer seu cenário (tipo de app, como deploya hoje, etc).]


### 📁 Estrutura de pastas recomendada:

php

CopiarEditar

`project-root/ │ ├── docker-compose.yml ├── nginx/ │   └── default.conf ├── certbot/ │   ├── conf/       # Armazena certificados SSL │   └── www/        # Pasta usada pelo Certbot para validação`

---

### 🐳 docker-compose.yml

yaml

CopiarEditar

`version: '3'  services:   app:     image: sua-imagem-api     container_name: app     expose:       - "3000"     restart: always    nginx:     image: nginx:latest     container_name: nginx     ports:       - "80:80"       - "443:443"     volumes:       - ./nginx/default.conf:/etc/nginx/conf.d/default.conf       - ./certbot/conf:/etc/letsencrypt       - ./certbot/www:/var/www/certbot     depends_on:       - app     restart: always    certbot:     image: certbot/certbot     container_name: certbot     volumes:       - ./certbot/conf:/etc/letsencrypt       - ./certbot/www:/var/www/certbot     entrypoint: >       sh -c "certbot certonly --webroot --webroot-path=/var/www/certbot        --email seu-email@example.com --agree-tos --no-eff-email        -d seu-dominio.com && echo Certificado gerado"`

---

### 📄 nginx/default.conf

nginx

CopiarEditar

`server {     listen 80;     server_name seu-dominio.com;      location /.well-known/acme-challenge/ {         root /var/www/certbot;     }      location / {         return 301 https://$host$request_uri;     } }  server {     listen 443 ssl;     server_name seu-dominio.com;      ssl_certificate /etc/letsencrypt/live/seu-dominio.com/fullchain.pem;     ssl_certificate_key /etc/letsencrypt/live/seu-dominio.com/privkey.pem;      location / {         proxy_pass http://app:3000;         proxy_http_version 1.1;         proxy_set_header Host $host;         proxy_set_header X-Real-IP $remote_addr;         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;     } }`

---

### 🚀 Como usar

1. **Troque `seu-dominio.com` e `seu-email@example.com` pelos seus.**
    
2. Certifique-se de que a **porta 80 e 443** estão abertas na AWS (Security Groups).
    
3. Rode os containers:
    

bash

CopiarEditar

`docker-compose up -d`

4. Gere o certificado:
    

bash

CopiarEditar

`docker-compose run certbot`

5. Reinicie o NGINX para começar a usar o SSL:
    

bash

CopiarEditar

`docker-compose restart nginx`

---

### 🔁 Renovação automática do SSL (opcional)

Você pode adicionar um **cron job** para rodar a renovação:

bash

CopiarEditar

`0 0 * * * docker-compose run certbot renew && docker-compose restart nginx`

---

Se quiser, posso montar isso com base na **sua aplicação específica** e domínio. Só me passar os dados (porta, domínio, etc).


## ✅ **O que o NGINX faz nesse caso**

No contexto do `docker-compose`, o NGINX atua como:

1. **Reverse Proxy** – encaminha requisições da internet para o container do seu app (`app:3000`).
    
2. **HTTPS Gateway** – lida com certificados SSL (Let’s Encrypt).
    
3. **Firewall básico** – pode limitar métodos, IPs, rate limit, etc.
    
4. **Servidor de redirecionamento** – redireciona HTTP → HTTPS.
    

---

## 🧭 **Fluxo completo**

1. Usuário acessa `https://seu-dominio.com`.
    
2. O NGINX (porta 443) recebe a requisição.
    
3. Ele valida o certificado SSL e encaminha a requisição para `app:3000`.
    
4. O app responde, e o NGINX devolve a resposta ao cliente.
    

---

## 📄 Como editar a configuração do NGINX

Abra o arquivo:

arduino

CopiarEditar

`nginx/default.conf`

E edite conforme seu uso. Exemplos:

---

### 1. **Servir frontend e backend separados:**

nginx

CopiarEditar

`server {     listen 443 ssl;     server_name seu-dominio.com;      ssl_certificate /etc/letsencrypt/live/seu-dominio.com/fullchain.pem;     ssl_certificate_key /etc/letsencrypt/live/seu-dominio.com/privkey.pem;      location /api/ {         proxy_pass http://app:3000/;     }      location / {         root /usr/share/nginx/html;         index index.html;     } }`

---

### 2. **Limitar acesso por IP:**

nginx

CopiarEditar

`location /admin {     allow 123.456.789.10;     deny all; }`

---

### 3. **GZIP, cache e performance:**

nginx

CopiarEditar

`gzip on; gzip_types text/plain application/json;  location / {     proxy_pass http://app:3000;     proxy_set_header Cache-Control "no-cache"; }`

---

## 🚦 Comandos úteis para gerenciar NGINX no Docker

bash

CopiarEditar

`# Subir os containers docker-compose up -d  # Ver logs do NGINX docker logs nginx  # Reiniciar apenas o NGINX docker-compose restart nginx  # Ver se NGINX está rodando dentro do container docker exec -it nginx nginx -t  # Testa config docker exec -it nginx nginx -s reload  # Recarrega config`

---

## 🔒 HTTPS com Certbot: primeiros passos

Se ainda não rodou o Certbot (para criar SSL):

bash

CopiarEditar

`docker-compose run certbot`

Se tudo estiver certo, o NGINX vai funcionar com HTTPS automaticamente depois disso.

---

## ❓Dica extra

Se quiser **usar NGINX fora do Docker**, também posso te mostrar como instalar e configurar direto numa instância EC2 Ubuntu, por exemplo.


## 🧰 **Pré-requisitos**

- Uma instância EC2 **Ubuntu** rodando (ex: Ubuntu 20.04 ou 22.04)
    
- Acesso por SSH
    
- As portas **80** e **443** abertas no **Security Group**
    
- Um domínio apontado para o IP da sua EC2 (ex: via Route 53 ou Cloudflare)
    

---

## ⚙️ **Passo a passo para instalar o NGINX**

### 1. **Acesse sua instância EC2**

bash

CopiarEditar

`ssh -i "sua-chave.pem" ubuntu@seu-ip`

---

### 2. **Atualize os pacotes**

bash

CopiarEditar

`sudo apt update && sudo apt upgrade -y`

---

### 3. **Instale o NGINX**

bash

CopiarEditar

`sudo apt install nginx -y`

---

### 4. **Verifique se está funcionando**

bash

CopiarEditar

`sudo systemctl status nginx`

Você pode acessar o IP da EC2 no navegador:  
👉 http://SEU-IP

---

## 🔐 **(Opcional) Instalar HTTPS com Let's Encrypt (Certbot)**

### 5. **Instale o Certbot**

bash

CopiarEditar

`sudo apt install certbot python3-certbot-nginx -y`

---

### 6. **Configure seu domínio para apontar para a EC2**

Verifique com:

bash

CopiarEditar

`ping seu-dominio.com`

---

### 7. **Obtenha o certificado SSL**

bash

CopiarEditar

`sudo certbot --nginx -d seu-dominio.com -d www.seu-dominio.com`

👉 Isso edita automaticamente o arquivo de config do NGINX e adiciona HTTPS.

---

### 8. **Renovação automática**

Certbot já cria um cron job para você. Para testar:

bash

CopiarEditar

`sudo certbot renew --dry-run`

---

## 📄 Arquivo de configuração NGINX (manual)

Editável com:

bash

CopiarEditar

`sudo nano /etc/nginx/sites-available/default`

Exemplo básico:

nginx

CopiarEditar

`server {     listen 80;     server_name seu-dominio.com www.seu-dominio.com;      return 301 https://$host$request_uri; }  server {     listen 443 ssl;     server_name seu-dominio.com www.seu-dominio.com;      ssl_certificate /etc/letsencrypt/live/seu-dominio.com/fullchain.pem;     ssl_certificate_key /etc/letsencrypt/live/seu-dominio.com/privkey.pem;      location / {         proxy_pass http://localhost:3000; # ou sua aplicação         proxy_set_header Host $host;         proxy_set_header X-Real-IP $remote_addr;     } }`

---

### 9. **Reinicie o NGINX após alterações**

bash

CopiarEditar

`sudo nginx -t   # Testa se não há erro sudo systemctl reload nginx`

---

## 🧪 Testes

- `http://seu-dominio.com` → Deve redirecionar para `https://...`
    
- `https://seu-dominio.com` → Deverá carregar sua aplicação (ou o default do NGINX)