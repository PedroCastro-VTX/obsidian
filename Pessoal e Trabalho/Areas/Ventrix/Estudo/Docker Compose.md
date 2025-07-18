#estudo #ventrix
#docker 
### **Docker Compose**

Docker Compose é uma ferramenta complementar ao Docker que facilita a definição e execução de aplicações com múltiplos containers.

- **Como funciona:** você cria um arquivo `docker-compose.yml` que descreve os serviços (containers), redes e volumes necessários.
- **Benefício:** inicializar tudo com um só comando (`docker-compose up`).
- **Exemplo típico:** uma aplicação com backend (Node.js), frontend (React) e banco de dados (PostgreSQL), todos em containers distintos.

### **1. Como começar com Docker Compose**

**Passos iniciais:**

1. Docker Compose já vem junto com o Docker Desktop.
    
2. Crie um arquivo chamado `docker-compose.yml`, por exemplo:
    
    yaml
    `version: '3' services:   web:     image: nginx     ports:       - "8080:80"`
    
3. Rode o serviço com:
    
    bash
    `docker-compose up`
    
4. Explore mais: adicione bancos de dados, apps backend e frontend.
    
5. Guia útil: Compose Getting Started