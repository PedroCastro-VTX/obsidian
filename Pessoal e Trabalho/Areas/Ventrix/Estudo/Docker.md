#estudo #ventrix
#docker 

### **Docker**

Docker é uma plataforma que utiliza containers para empacotar aplicações com todas as suas dependências (bibliotecas, ferramentas, código, etc.), garantindo que elas funcionem da mesma forma em qualquer ambiente — seja no desenvolvedor, servidor ou produção.

- **Principais vantagens:** portabilidade, consistência e isolamento.
- **Funcionamento:** usa imagens (modelos prontos) para criar containers (instâncias em execução).
- **Casos de uso:** desenvolvimento local, CI/CD, microserviços, testes automatizados.

### **1. Como começar com Docker**

**Passos iniciais:**

1. **Instale o Docker Desktop** (Windows/macOS) ou `docker-ce` (Linux).
    
    - Site oficial: https://www.docker.com/get-started
        
2. Rode seu primeiro container:
	 bash
    `docker run hello-world`
    
    Isso testa se o Docker está funcionando.
    
3. Aprenda os comandos básicos:
    
    - `docker build`, `docker run`, `docker ps`, `docker exec`, `docker images`, `docker stop`, etc.
        
4. Crie um `Dockerfile` simples para empacotar um app (ex: Node.js ou Python).
    
5. Use a documentação oficial: [Docker Docs](https://docs.docker.com/)

