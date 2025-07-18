#docker
#estudo 
#ventrix 

## ✅ **1. Montar um volume com `volumes:` (RECOMENDADO para dev)**

Essa é a maneira ideal de manter os arquivos do host sincronizados com o container:

### Exemplo `docker-compose.yml`:

yaml

CopiarEditar

`services:   app:     image: sua-imagem     volumes:       - ./meus-arquivos:/app  # ./local → /app no container`

Com isso:

- Qualquer mudança em `./meus-arquivos` (no host) será refletida **instantaneamente** no container.
    
- Bom para arquivos de código, HTML, config etc.
    

---

## 🔁 **2. Copiar arquivos manualmente com `docker cp`**

Se você quer copiar um arquivo pontual, sem montar volume:

### Copiar do **host para o container**:

bash

CopiarEditar

`docker cp ./meuarquivo.txt nome_ou_id_do_container:/caminho/no/container/`

### Copiar do **container para o host**:

bash

CopiarEditar

`docker cp nome_ou_id_do_container:/caminho/no/container/meuarquivo.txt .`

---

## 🛠️ **3. Editar diretamente dentro do container**

Se for algo rápido ou pontual, você pode "entrar" no container e editar:

bash

CopiarEditar

`docker exec -it nome_ou_id_do_container bash # ou docker exec -it nome_ou_id_do_container sh`

Depois, use `nano`, `vim` ou `vi` para editar arquivos:

bash

CopiarEditar

`cd /app nano config.json`

---

## 🧱 **4. Rebuild da imagem com novos arquivos (para produção)**

Se você quer mudar arquivos em containers de produção **sem volumes**, edite os arquivos localmente e **rebuild a imagem**:

bash

CopiarEditar

`# Rebuild com novos arquivos docker-compose build  # Ou sem compose docker build -t sua-imagem .  # Substitui o container rodando docker-compose up -d --force-recreate`

---

## 👀 Quer saber qual arquivo mudou?

Você pode usar:

bash

CopiarEditar

`docker diff nome_ou_id_do_container`

Ele mostra mudanças feitas dentro do container após ser iniciado.


### ✅ **Situação comum:**

Você tem uma EC2 com Docker e sua aplicação está rodando com `docker-compose up -d`.

---

## 🧭 Caminhos possíveis para atualizar:

---

### **✔️ 1. Atualizar arquivos sem rebuild (se usa volume mapeado)**

Se você mapeou um volume, tipo:

yaml

CopiarEditar

`volumes:   - ./app:/app`

Então **basta atualizar os arquivos no EC2** (via SSH, Git pull, SCP, etc), e o container **vai refletir automaticamente**, sem precisar reiniciar nada.

> Use esse modelo em dev.

---

### **✔️ 2. Atualizar a imagem e redeployar (produção)**

Se sua aplicação está empacotada no container (sem volumes), o jeito mais limpo é:

### **Passos:**

1. **SSH na EC2**
    

bash

CopiarEditar

`ssh -i "sua-chave.pem" ubuntu@seu-ip`

2. **Atualize o código (Git pull, upload, etc)**
    
3. **Rebuild a imagem**
    

bash

CopiarEditar

`docker-compose build`

4. **Reinicie os containers com nova versão**
    

bash

CopiarEditar

`docker-compose up -d --force-recreate`

---

### **✔️ 3. Puxar uma imagem nova (ex: de registry)**

Se você faz push das suas imagens no Docker Hub, GitHub Packages, ECR (Amazon), etc:

bash

CopiarEditar

`docker-compose pull         # baixa a imagem nova docker-compose up -d        # reinicia com a nova imagem`

---

## 🧹 Dica Extra: limpar imagens antigas

bash

CopiarEditar

`docker image prune -a`

---

## 📦 Exemplo completo de atualização em produção:

bash

CopiarEditar

`# SSH na EC2 ssh -i chave.pem ubuntu@ec2...  # Ir para o diretório da aplicação cd /home/ubuntu/app  # Puxar últimas alterações git pull origin main  # Rebuild da imagem docker-compose build  # Restartando containers docker-compose up -d --force-recreate  # Verificar logs docker-compose logs -f`


### ✅ CENÁRIO

Você tem:

- Um servidor rodando na **EC2**, com sua aplicação em **containers via Docker** (por exemplo com `docker run` ou `docker-compose`).
    
- Precisa **atualizar arquivos (como código fonte, configs, assets)** dentro de um container.
    

---

## 🔧 MÉTODO 1 – **Volumes (Recomendado em desenvolvimento ou CI/CD)**

Monte um volume local do host (EC2) no container para que o código seja atualizado no container automaticamente.

### 1. Estrutura de pasta no host:

bash

CopiarEditar

`/home/ubuntu/app/   |- docker-compose.yml   |- nginx/       |- default.conf   |- app/       |- index.html       |- main.py`

### 2. Exemplo de `docker-compose.yml` com volume:

yaml

CopiarEditar

`version: '3' services:   web:     image: nginx:latest     ports:       - "80:80"     volumes:       - ./nginx/default.conf:/etc/nginx/conf.d/default.conf       - ./app:/usr/share/nginx/html`

### ✅ Vantagens:

- Você **só precisa substituir os arquivos em `./app`**, e o container vai refletir as mudanças.
    
- Rápido e prático.
    

---

## 🔧 MÉTODO 2 – **Atualizando manualmente no container**

Se você já subiu o container, pode copiar novos arquivos com `docker cp`.

### 1. Envie o arquivo para o host:

bash

CopiarEditar

`scp index.html ubuntu@<IP_EC2>:/home/ubuntu/`

### 2. Copie para o container:

bash

CopiarEditar

`docker cp /home/ubuntu/index.html <nome_ou_id_do_container>:/usr/share/nginx/html/index.html`

> Use `docker ps` para descobrir o ID ou nome do container.

---

## 🔧 MÉTODO 3 – **Atualizando com `docker-compose up --build`**

Se seu código estiver em um Dockerfile, você pode rebuildar a imagem com as atualizações.

### 1. Atualize os arquivos localmente

### 2. Suba novamente:

bash

CopiarEditar

`docker-compose up --build -d`

---

## 🔧 MÉTODO 4 – **CI/CD (Ex: GitHub Actions ou AWS CodeDeploy)**

Automatize o processo: um push no Git pode ativar uma pipeline que reconstrói e sobe os containers na EC2.

---

## 💡 Dica Extra: Reiniciar container após alteração

Se você alterou apenas o volume ou os arquivos, talvez queira reiniciar:

bash

CopiarEditar

`docker-compose restart`