### **1. AWS com Linux – Usando containers para banco de dados (PostgreSQL)**

- Se você tiver **um único micro (EC2)**, pode rodar **um container Docker com PostgreSQL**.
    
- Use volumes para persistência de dados:
    
    bash
    
    `docker run -d --name postgres \   -e POSTGRES_PASSWORD=senha \   -v /dados/postgres:/var/lib/postgresql/data \   -p 5432:5432 \   postgres:15`



---

### ✅ **2. Linux + Nginx com ou sem Docker**

- **Com Docker:** é mais portátil e facilita o deploy.
    
- **Sem Docker:** mais controle do sistema, mas menos flexível.
    
- Recomendo usar **Docker** para padronizar ambientes.
    

---

### ✅ **3. Servidor de Autenticação e Dados em containers**

- Se você tem serviços próprios de autenticação (ex: OAuth, JWT), **containerizar** facilita o deploy.
    
- Exemplo: Auth Server + API + DB → cada um em seu container.
    

---

### ✅ **4. Subir 4 servidores na AWS (EC2 + Linux):**

Você pode usar 1 micro EC2 simples por serviço:

1. **Servidor de Autenticação**
    
2. **Servidor de Dados / API**
    
3. **PostgreSQL**
    
4. **Nginx (com HTTPS + balanceamento)**
    

> Ou usar **1 EC2 com múltiplos containers**, se o tráfego for baixo.

---

### ✅ **5. Docker no Linux**

- Instale Docker com:
    
    bash
    
    `sudo apt install docker.io sudo systemctl enable docker sudo usermod -aG docker $USER`
    
- Para compor múltiplos serviços:
    
    - Use **Docker Compose**.
        

---

### ✅ **6. Grafana para monitoramento**

- Você pode usar o **Grafana + Prometheus** em containers para monitorar CPU, RAM, uso de disco e saúde dos containers/servidores.
    

---

### ✅ **7. Nginx: Load Balancer + HTTPS**

- **Balanceamento**: use `upstream` em Nginx:
    
    nginx
    
    `upstream app {   server 10.0.0.2:3000;   server 10.0.0.3:3000; }  server {   listen 80;   location / {     proxy_pass http://app;   } }`
    
- **HTTPS**: use Let’s Encrypt com Certbot ou instale seus próprios certificados.

___
### ✅ **7.1 Nginx: Load Balancer + HTTPS ( Verificar o EC2 )**




---

### ✅ **8. AWS + Docker**

- Você pode rodar Docker em EC2 diretamente.
    
- Ou usar **ECS (Elastic Container Service)**, que orquestra containers sem precisar gerenciar o sistema operacional.
    
- Para facilitar deploys, use:
    
    - **EC2 + Docker Compose**, ou
        
    - **ECS + Fargate** (serverless)
        

---

### ✅ **9. Atualizações**

- **Docker containers** precisam ser reconstruídos para atualizar:
    
    bash
    
    `docker pull postgres:latest docker stop antigo && docker rm antigo docker run ... (novo)`
    
- O mesmo vale para Nginx ou qualquer serviço: você baixa a nova imagem, e reinicia o container.
    

---

### ✅ **10. Começar Docker no seu PC**

- Instale o Docker Desktop ou use WSL2 no Windows com Ubuntu.
    
- Crie containers localmente, depois leve para a AWS.
    

---

### ✅ **11. Guia de uso dos servidores (documentação)**

Você deve registrar:

- Qual serviço roda em qual servidor
    
- Como subir os containers
    
- Como escalar ou atualizar
    
- Como monitorar (Grafana)
    
- Como fazer backup (ex: dump do Postgres)

### ✅ **12. Terraform**

#### 1. **Criar os 4 servidores (EC2) automaticamente**

Em vez de criar manualmente cada servidor no painel da AWS, com o Terraform você pode escrever algo como:

hcl

`resource "aws_instance" "nginx" {   ami           = "ami-xxxxxxxx"   instance_type = "t2.micro"   tags = {     Name = "nginx-server"   } }`

Depois é só rodar:

bash

`terraform init terraform apply`

#### 2. **Configurar regras de segurança (Security Groups)**

Você pode definir que porta 80 (HTTP), 443 (HTTPS) e 5432 (PostgreSQL) estejam abertas apenas para os serviços certos.

#### 3. **Definir variáveis para facilitar replicação**

Quer subir 3 servidores de API iguais? Só muda uma variável. Isso dá escalabilidade e padronização.

#### 4. **Provisionar junto com Docker (usando scripts ou Ansible depois)**

O Terraform pode criar as VMs e até rodar um script de bootstrap para instalar Docker e rodar os containers automaticamente.

---

### 🧠 **Por que usar Terraform aqui é vantajoso:**

- **Infraestrutura como código** → tudo versionado em Git
    
- **Reprodutível** → subir tudo igual no dev, staging e produção
    
- **Auditável** → você vê o que foi criado e por quem
    
- **Modular** → você pode ter um módulo só para "banco de dados", outro para "nginx", etc.
    

---

### 🚀 Exemplo do fluxo com Terraform:

1. Escreve os arquivos `.tf` com os recursos (EC2, SGs, VPC, etc.)
    
2. Aponta os scripts de inicialização (user-data ou Ansible)
    
3. Executa `terraform apply`
    
4. Servidores sobem já com Docker pronto para rodar seus containers