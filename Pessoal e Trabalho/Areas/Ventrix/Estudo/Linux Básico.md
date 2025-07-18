#estudo #ventrix

### ✅ **1. O que é Linux?**

Linux é um sistema operacional baseado em Unix, gratuito e de código aberto. É o mais usado em servidores, sistemas embarcados, e também muito presente no mundo da programação e DevOps.

---

### 🗂️ **2. Estrutura de Diretórios Básica**

- `/home`: diretórios dos usuários
    
- `/etc`: arquivos de configuração
    
- `/var`: arquivos variáveis (logs, cache)
    
- `/usr`: programas e bibliotecas de usuários
    
- `/bin`, `/sbin`: binários essenciais
    
- `/root`: home do superusuário (root)
    

---

### 🧑‍💻 **3. Comandos essenciais para o terminal**

| Comando              | Descrição                             |
| -------------------- | ------------------------------------- |
| `pwd`                | Mostra o caminho atual (diretório)    |
| `ls`                 | Lista arquivos e pastas               |
| `cd nome_da_pasta`   | Entra em uma pasta                    |
| `mkdir nova_pasta`   | Cria uma nova pasta                   |
| `touch arquivo.txt`  | Cria um novo arquivo vazio            |
| `rm arquivo.txt`     | Remove arquivo                        |
| `cp origem destino`  | Copia arquivos ou pastas              |
| `mv origem destino`  | Move ou renomeia arquivos/pastas      |
| `cat arquivo.txt`    | Mostra o conteúdo do arquivo          |
| `nano arquivo.txt`   | Editor de texto no terminal (simples) |
| `sudo comando`       | Executa comando como superusuário     |
| `chmod +x script.sh` | Dá permissão de execução              |

---

### ⚙️ **4. Gerenciamento de Pacotes**

- **Ubuntu/Debian:**
    
    bash
    
    CopiarEditar
    
    `sudo apt update sudo apt install nome-do-pacote`
    
- **RedHat/CentOS:**
    
    bash
    
    CopiarEditar
    
    `sudo yum install nome-do-pacote`
    

---

### 🔍 **5. Monitoramento e processos**

- `top` ou `htop`: mostra uso da CPU/RAM em tempo real
    
- `ps aux`: lista todos os processos
    
- `kill PID`: finaliza processo pelo ID
    
- `df -h`: espaço em disco
    
- `free -h`: uso de memória RAM
    

---

### 📁 **6. Permissões**

- Cada arquivo/pasta tem permissões de leitura (r), escrita (w) e execução (x) para:
    
    - **Usuário**
        
    - **Grupo**
        
    - **Outros**
        
- Comando para alterar permissões:
    
    bash
    
    CopiarEditar
    
    `chmod 755 arquivo.sh`
    

---

### 📚 **7. Como começar a praticar**

1. Instale uma distribuição Linux (como Ubuntu) em uma VM, WSL (Windows), ou use um serviço online como https://www.tutorialspoint.com/unix_terminal_online.php.
    
2. Pratique comandos simples, edite arquivos com `nano` ou `vim`.
    
3. Explore scripts em `bash` para automatizar tarefas.
    
4. Use o comando `man` para ver a documentação de qualquer comando:
    
    bash
    
    CopiarEditar
    
    `man ls`