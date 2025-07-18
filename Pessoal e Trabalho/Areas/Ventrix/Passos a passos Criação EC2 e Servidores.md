#servidores
#ventrix 
#passo-a-passo

## ✅ Etapas para criar uma instância Linux na AWS (via Console)

### 1. **Acesse o EC2**

- Vá para o painel da AWS: [https://console.aws.amazon.com/ec2/](https://console.aws.amazon.com/ec2/)
    
- Clique em **"Launch Instance"**
    

---

### 2. **Configurações básicas**

#### a. **Nome da instância**

- Exemplo: `meu-servidor-linux`
    

#### b. **Imagem de máquina (AMI)**

Escolha uma dessas:

- **Amazon Linux 2** _(leve e otimizada para AWS)_
    
- **Ubuntu 22.04 LTS** _(boa para desenvolvimento geral)_
    
#### c. **Tipo de instância**

- **t2.micro** (grátis no Free Tier, ideal para testes)
    
- **t3.micro** (mais recente, se disponível no seu Free Tier)
    

---

### 3. **Chave de acesso (SSH)**

- Crie ou selecione uma **chave PEM**
    
- Baixe e guarde com cuidado (você usará para se conectar via terminal/SSH)
    

---

### 4. **Configuração de rede (Security Group)**

Crie ou use um grupo de segurança com essas **regras de entrada**:

|Tipo|Protocolo|Porta|Origem|
|---|---|---|---|
|SSH|TCP|22|Seu IP (recomendado)|
|HTTP (opcional)|TCP|80|0.0.0.0/0|
|HTTPS (opcional)|TCP|443|0.0.0.0/0|
### 5. **Disco**

- 8 GB padrão (você pode ajustar)
    
- Tipo: **gp3** (SSD geral)
    

---

### 6. **Lançar instância**

- Clique em **Launch**
    
- Aguarde a instância iniciar
    

---

## 💻 Conectando via SSH (Linux/macOS/WSL)

chmod 400 sua-chave.pem
ssh -i sua-chave.pem ec2-user@IP-PÚBLICO

    Para Ubuntu, use ubuntu@IP ao invés de ec2-user@IP

## 🌐 1. **Apontar o domínio para a instância EC2**

### a. Obtenha o **IP público da sua instância**

- No painel do EC2 → veja o campo **“IPv4 Public IP”**
    

### b. No seu provedor de domínio (GoDaddy, Registro.br, Cloudflare, etc):

- Crie um **registro A**:
    
    - Nome: `@` ou `www` (ou `app`, se quiser `app.seudominio.com`)
        
    - Tipo: `A`
        
    - Valor: **IP público da instância**
        
    - TTL: 300 (ou automático)
        

> Exemplo: @      A     13.201.45.123
www    A     13.201.45.123

## 🧱 2. **Instalar o NGINX na EC2**

Depois de conectar via SSH:

# Para Amazon Linux 2:
sudo yum update -y
sudo amazon-linux-extras install nginx1 -y
sudo systemctl enable nginx
sudo systemctl start nginx

# Ou para Ubuntu:
sudo apt update && sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx

Depois, acesse seu IP no navegador: `http://seu-ip` — deve aparecer a tela padrão do NGINX.


## 🔒 3. **Instalar HTTPS com Certbot (Let's Encrypt)**

### a. Instalar Certbot:

**Amazon Linux 2**:

sudo yum install -y epel-release
sudo yum install -y certbot python2-certbot-nginx

**Unbutu**
sudo apt install certbot python3-certbot-nginx -y

### b. Obter o certificado:
`sudo certbot --nginx`

Siga os passos:

- Informe o domínio (ex: `meusite.com`)
    
- Confirme o redirecionamento de HTTP → HTTPS
    

### c. Verificar renovação automática:
`sudo certbot renew --dry-run`

## 🌍 4. **Testar o domínio**

Acesse `https://seusite.com` no navegador. Se tudo estiver certo:

- O certificado SSL será válido (https)
    
- O NGINX exibirá sua página padrão ou o conteúdo que você configurar


## 🐳 1. **Instalar o Docker na sua VM Linux**

### 👉 Para Ubuntu:

sudo apt update
sudo apt install -y docker.io
sudo systemctl enable docker
sudo systemctl start docker

### 👉 Para Amazon Linux 2:

sudo yum update -y
sudo amazon-linux-extras enable docker
sudo yum install -y docker
sudo systemctl start docker
sudo systemctl enable docker
Adicione seu usuário ao grupo `docker` para evitar usar `sudo`:

`sudo usermod -aG docker ec2-user # ou ubuntu`
⚠️ Faça logout e login de novo para aplicar.

## 📦 2. **Rodar sua aplicação em Docker**

Exemplo: você quer rodar um app Node.js, Flask, ou qualquer backend.
docker run -d \
  --name meuapp \
  -p 3000:3000 \
  minha-imagem:latest
Substitua `minha-imagem` pelo nome da sua imagem local ou do Docker Hub.

## 🌐 3. **Configurar NGINX como proxy reverso**

Crie o arquivo de configuração:

sudo nano /etc/nginx/conf.d/seusite.conf
Exemplo de configuração para um app rodando em `localhost:3000`:

server {
    listen 80;
    server_name seusite.com www.seusite.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Reinicie o NGINX:
sudo nginx -t
sudo systemctl reload nginx
## 🔒 4. **Instalar HTTPS (SSL) com Certbot**

Se ainda não fez, instale o Certbot:

### Ubuntu:

sudo apt install certbot python3-certbot-nginx -y

### Amazon Linux 2:

sudo yum install -y certbot python2-certbot-nginx

### Execute o Certbot:

sudo certbot --nginx

Escolha seu domínio e aceite redirecionar HTTP para HTTPS.

Excelente pergunta! Você está diante de uma decisão **de arquitetura**: usar **múltiplas VMs (EC2)** ou **containers Docker (microserviços)**. Aqui está um guia direto e estratégico pra decidir **quando usar VM ou Docker para cada serviço** que você mencionou:

---
Excelente pergunta! Você está diante de uma decisão **de arquitetura**: usar **múltiplas VMs (EC2)** ou **containers Docker (microserviços)**. Aqui está um guia direto e estratégico pra decidir **quando usar VM ou Docker para cada serviço** que você mencionou:
## ⚙️ Visão Geral dos 4 Componentes

| Serviço                            | Docker (microserviço) | VM separada (EC2)                        | Recomendação                     |
| ---------------------------------- | --------------------- | ---------------------------------------- | -------------------------------- |
| **1. Servidor de Autenticação**    | ✅ Sim                 | Opcional                                 | Docker, isolado                  |
| **2. Servidor de Dados/API**       | ✅ Sim                 | Opcional, se tráfego muito alto          | Docker (com ajuste de escala)    |
| **3. PostgreSQL**                  | ⚠️ Sim, com cuidados  | ✅ Sim                                    | Melhor em VM ou RDS              |
| **4. NGINX (balanceador + HTTPS)** | ✅ Sim                 | ⚠️ Sim, se alta carga ou múltiplas zonas | Docker simples ou em uma VM leve |
## 🧠 Decisão prática (baseada em escalabilidade, custo e segurança)

### ✅ O ideal para **fase inicial / MVP / produção controlada**:

| Instância EC2           | O que roda nela (via Docker)                     |
| ----------------------- | ------------------------------------------------ |
| **EC2 #1 (App Server)** | Autenticação + API (em containers separados)     |
| **EC2 #2 (Infra)**      | NGINX (reverse proxy + HTTPS), Traefik ou Caddy  |
| **EC2 #3 (Banco)**      | PostgreSQL (sem Docker, direto no SO ou via RDS) |
|                         |                                                  |
|                         |                                                  |
### 🔁 Escalabilidade futura:

- Você pode escalar horizontalmente os containers de API/autenticação (Docker Swarm ou ECS).
    
- Pode usar **Amazon RDS** para gerenciar o PostgreSQL com backups automáticos, failover etc.
    
- O NGINX pode ser substituído por **Application Load Balancer (ALB)** da AWS.
    

---

🧱 Exemplo de stack com Docker Compose

version: '3.8'
services:
  auth:
    build: ./auth
    ports: [ "4000:4000" ]
    networks: [ "backend" ]

  api:
    build: ./api
    ports: [ "5000:5000" ]
    networks: [ "backend" ]

  nginx:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports: [ "80:80", "443:443" ]
    depends_on: [ "auth", "api" ]
    networks: [ "backend" ]

networks:
  backend:
  
PostgreSQL ficaria **fora** desse `docker-compose` em uma outra VM ou via RDS.

## ✅ Conclusão (recomendado para você)

🔒 **PostgreSQL**  
→ Rodar fora do Docker, em uma EC2 dedicada ou Amazon RDS  
🎯 **Motivo**: performance, segurança e persistência

📦 **Autenticação + API**  
→ Rodar juntos em containers, orquestrados com Docker Compose ou ECS

🌐 **NGINX com HTTPS e balanceamento**  
→ Pode estar em um container simples ou numa pequena VM só para isso


## 🔍 EC2 Micro **≠** Máquina Virtual diferente

- Na AWS, **cada instância EC2 é uma máquina virtual separada**.
    
- Quando você escolhe uma **t2.micro / t3.micro / t3.small**, está apenas definindo o **tamanho (CPU/RAM)** daquela **VM individual**.
    
- Ou seja: **t2.micro = uma VM pequena**, não um "tipo especial" de container ou microserviço.

## ✅ Então, respondendo diretamente:

Se você criar 3 instâncias EC2 (ex: uma para Nginx, uma para PostgreSQL e outra para API):

- Você terá **3 máquinas virtuais separadas**
    
- Pode usar o tipo **micro, small ou medium**, dependendo da carga
    
- Pode rodar **1 ou mais containers Docker dentro de cada uma**

## 🧠 Estratégia recomendada com base no uso comum:

| Serviço            | EC2 separada?           | Tipo sugerido (Free Tier ou leve) | Docker?                                   |
| ------------------ | ----------------------- | --------------------------------- | ----------------------------------------- |
| NGINX + Certbot    | ✅ Sim (ou junto da API) | t2.micro ou t3.micro              | ✅ Sim (leve)                              |
| Autenticação + API | ✅ Sim                   | t3.micro ou t3.small              | ✅ Sim                                     |
| PostgreSQL         | ✅ Sim (ou RDS)          | t3.small ou t3.medium             | ⚠️ Não recomendado em Docker, se possível |

## 🧰 Exemplo de setup com instâncias pequenas (produção leve / MVP):

| Instância EC2 | Serviços                              | Tipo                  |
| ------------- | ------------------------------------- | --------------------- |
| `ec2-nginx`   | NGINX + Certbot + Proxy               | t2.micro              |
| `ec2-app`     | API + Autenticação (Docker)           | t3.small              |
| `ec2-db`      | PostgreSQL (sem Docker, direto no SO) | t3.small ou t3.medium |

## Alternativas:

- Se quiser **escalar automaticamente** e pagar menos por ociosidade, pode usar:
    
    - **ECS + Fargate** (containers gerenciados pela AWS)
        
    - **Lambda + API Gateway** (para APIs sem servidor)
        

---

## 🔁 1. **Auto Scaling com EC2 (VMs)**

### Como funciona:

Você cria um **grupo de Auto Scaling**, define:

- **Imagem de máquina (AMI)** com seu app
    
- **Limites de CPU, memória ou tráfego**
    
- Quantidade mínima e máxima de instâncias
    

A AWS então:

- Cria novas EC2 automaticamente se a carga subir
    
- Desliga as EC2 extras quando a carga cai
    

### Exemplo:

> Se a CPU média passar de 70% por 5 minutos, sobe mais 1 instância EC2 com a mesma imagem.

### Você precisa de:

- **EC2 Launch Template**
    
- **Auto Scaling Group**
    
- **Application Load Balancer (ALB)** ou NGINX como load balancer
    

---

## 🐳 2. **Escalar com Docker usando ECS + Fargate (serverless containers)**

Essa é a **alternativa moderna** para quem quer escalar containers sem gerenciar servidores.

### Vantagens:

- **Sem EC2 fixa** → você só paga pelos containers rodando
    
- **Auto scaling integrado** com métricas de CPU, memória ou requisições
    
- **Gerenciado pela AWS**, sem precisar configurar máquinas
    

### Como funciona:

- Você empacota seu app (API, auth, etc) em **containers**
    
- Sobe no **Amazon Elastic Container Service (ECS)** com **Fargate**
    
- Define regras de escala (ex: “se CPU > 60%, criar mais containers”)
    

> O balanceamento é feito com **ALB (Application Load Balancer)** que distribui o tráfego entre os containers.


## ⚖️ Comparativo rápido: Auto Scaling EC2 vs ECS + Fargate

|Recurso|EC2 com Auto Scaling|ECS + Fargate (containers)|
|---|---|---|
|Gerenciamento de VM|✅ Sim (você cuida)|❌ Não (AWS cuida)|
|Escalabilidade|✅ Boa|✅ Excelente|
|Complexidade inicial|🔧 Moderada|🔧 Leve/moderada|
|Performance customizada|✅ Alta (você escolhe)|🔄 Adaptável, depende da tarefa|
|Custo sob demanda|⚠️ Pode ser maior|✅ Paga só pelo uso real|
|Ideal para|Apps mais pesados|APIs, microsserviços, startups|
## 🧠 Recomendação prática pra você

Se você:

- Está montando uma arquitetura moderna e escalável
    
- Já está ou pretende usar Docker
    
- Quer evitar dor de cabeça com VMs
    

👉 **Use ECS + Fargate** com auto scaling e ALB.

> Exemplo: sua API e Auth em containers → escalam automaticamente com tráfego. Banco de dados continua fixo (em EC2 ou RDS).


## 🧱 Arquitetura Geral

| EC2                     | Função                          | Docker?                         | Tipo sugerido   |
| ----------------------- | ------------------------------- | ------------------------------- | --------------- |
| **EC2 #1: App Server**  | API + Autenticação (containers) | ✅ Sim                           | t3.small        |
| **EC2 #2: NGINX Proxy** | NGINX + Certbot (SSL) + Proxy   | ✅ Sim                           | t2.micro        |
| **EC2 #3: Banco**       | PostgreSQL                      | ❌ Não (direto no SO ou via RDS) | t3.small ou RDS |
# 🖥️ EC2 #1 — App Server (Auth + API)

### Estrutura de diretórios:

/home/ec2-user/app/
├── auth/
│   └── Dockerfile
├── api/
│   └── Dockerfile
├── docker-compose.yml

`docker-compose.yml`:

version: '3.8'

services:
  auth:
    build: ./auth
    ports:
      - "4000:4000"
    environment:
      - DB_URL=postgresql://user:pass@<db_ip>:5432/dbname
    restart: always
    networks:
      - backend

  api:
    build: ./api
    ports:
      - "5000:5000"
    environment:
      - DB_URL=postgresql://user:pass@<db_ip>:5432/dbname
    restart: always
    networks:
      - backend

networks:
  backend:

Substitua `<db_ip>` pelo IP privado da EC2 do banco.

# 🌐 EC2 #2 — NGINX Proxy com HTTPS
### Estrutura:

/home/ec2-user/nginx/
├── nginx.conf
├── docker-compose.yml

`nginx.conf` (exemplo básico):

events {}
http {
  server {
    listen 80;
    server_name seusite.com www.seusite.com;

    location /api/ {
      proxy_pass http://<app_server_ip>:5000/;
    }

    location /auth/ {
      proxy_pass http://<app_server_ip>:4000/;
    }
  }
}

`docker-compose.yml`:

version: '3.8'

services:
  nginx:
    image: nginx:latest
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    ports:
      - "80:80"
      - "443:443"
    restart: always

Depois, instale o **Certbot no host** (fora do container) com:

sudo apt install certbot python3-certbot-nginx -y
sudo certbot --nginx
 
# 🛢️ EC2 #3 — PostgreSQL

### Rodar diretamente no sistema (sem Docker):

sudo apt update
sudo apt install postgresql postgresql-contrib -y

### Configurar acesso:

- Edite o `postgresql.conf`:
 sudo nano /etc/postgresql/14/main/postgresql.conf

→ Altere `listen_addresses = '*'`

- Edite o `pg_hba.conf`:
sudo nano /etc/postgresql/14/main/pg_hba.conf

→ Adicione:
`host all all <app_server_private_ip>/32 md5`

-> Reinicie:
`sudo systemctl restart postgresql`

**Crie banco/usuário com:**
sudo -u postgres psql
CREATE USER user WITH PASSWORD 'pass';
CREATE DATABASE dbname;
GRANT ALL PRIVILEGES ON DATABASE dbname TO user;

## 📡 Comunicação entre instâncias

- As EC2s precisam estar **na mesma VPC** e **sub-rede** para comunicação via IP privado
    
- Libere as portas nos **Security Groups**:
    
    - EC2 API/Auth: portas 4000 e 5000 (acesso só do NGINX)
        
    - EC2 PostgreSQL: porta 5432 (acesso só do App Server)
        
    - EC2 NGINX: portas 80 e 443 (abertas para internet)

## 🔁 Auto Scaling e Load Balancer (futuro)

Mais pra frente, você pode:

- Criar um **Application Load Balancer (ALB)** para distribuir para múltiplas instâncias do App Server
    
- Substituir EC2 + NGINX por ECS + Fargate
    
- Mover PostgreSQL para **RDS** gerenciado

# CONFIGURAÇÕES EC2'S:

[[ec_2_docker_architecture.yaml]]

# 1. Criar Application Load Balancer (ALB) com HTTPS

### Passo 1: Criar um certificado SSL no AWS Certificate Manager (ACM)

- Vá no Console AWS > Certificate Manager
    
- Clique em **Provisionar certificados**
    
- Escolha **Solicitar um certificado público**
    
- Insira seu domínio (ex: `seusite.com` e `www.seusite.com`)
    
- Valide o domínio (por DNS ou email) — DNS é mais rápido, se seu domínio estiver na Route53
    
- Aguarde aprovação (pode levar alguns minutos)

### Passo 2: Criar o Load Balancer

- Console AWS > EC2 > Load Balancers > Criar Load Balancer
    
- Escolha **Application Load Balancer**
    
- Nomeie o ALB (ex: `app-alb`)
    
- Escolha esquema: **internet-facing**
    
- Defina as zonas de disponibilidade que sua VPC usa
    
- Em **Listeners**, deixe HTTP na porta 80 e adicione HTTPS na porta 443
    
- Em **Configuração SSL**, selecione o certificado do ACM criado

### Passo 3: Configurar grupos alvo (Target Groups)

- Crie um **Target Group** para suas EC2s do App Server (tipo: instâncias, protocolo HTTP, porta 5000 ou 4000)
    
- Adicione as instâncias EC2 com o serviço API e autenticação para esse target group
    
- Configure health checks na porta correta (ex: `/health` se tiver endpoint de status)

### Passo 4: Configurar regras do Listener HTTPS

- No Listener HTTPS (porta 443), direcione o tráfego para o Target Group criado
    
- Opcional: No Listener HTTP (porta 80), configure redirecionamento para HTTPS para forçar segurança

# 2. Configurar Security Groups para Load Balancer e EC2

- Crie Security Group para o ALB permitindo entrada nas portas 80 e 443
    
- Modifique Security Group das EC2 para permitir tráfego do SG do ALB nas portas 4000 e 5000

# 3. Criar regras de Auto Scaling

### Passo 1: Criar um Launch Template

- EC2 > Launch Templates > Criar template
    
- Configure a AMI, tipo de instância (ex: t3.small), chave SSH, Security Group, User Data (script para iniciar Docker Compose)
    

Exemplo de **User Data** para rodar Docker Compose na inicialização:

#!/bin/bash
cd /home/ec2-user/app
docker-compose up -d
### Passo 2: Criar Auto Scaling Group

- EC2 > Auto Scaling Groups > Criar grupo
    
- Selecione o Launch Template criado
    
- Configure zona e sub-redes (mínimo 1 instância, máximo conforme desejar)
    
- Associe o Target Group do ALB criado
    
- Defina políticas de escala:
    
    - Por exemplo, se CPU média > 70% por 5 minutos → adiciona +1 instância
        
    - Se CPU média < 40% por 10 minutos → remove -1 instância


# GRAFANA

### 1. Conectar Grafana ao CloudWatch

- No Grafana, adicione uma nova fonte de dados: escolha **CloudWatch**
    
- Configure as credenciais AWS (via IAM Role, Access Key ou perfil local)
    
- Se estiver rodando Grafana dentro da AWS (ex: EC2), pode usar **IAM Role** para facilitar

### 2. Criar dashboards

- Após conectar, você pode criar painéis que exibem métricas de CPU, memória, disco, rede, etc, para suas instâncias EC2
    
- Também pode monitorar grupos de Auto Scaling (métricas como número de instâncias, status de health check)
    
- Use painéis prontos da comunidade Grafana para EC2/Auto Scaling ou crie os seus personalizados

### 3. Alertas

- Grafana também permite configurar alertas com base em métricas, enviando notificações por email, Slack, Telegram, etc
    
- Você pode complementar os alarmes do CloudWatch com alertas customizados no Grafana

### Dicas extras

- Se quiser métricas mais detalhadas (ex: uso de memória, processos dentro do container), instale o **CloudWatch Agent** na EC2
    
- Para monitorar Docker e containers, pode usar o **Prometheus** com **node_exporter** e **cAdvisor**, enviando dados para Grafana