## Como permitir acesso do time à VM (na AWS, por exemplo): 

1. **Suba a instância normalmente.**
    
2. **Adicione as chaves públicas SSH de cada membro no arquivo:**
    
    bash
    
    `~/.ssh/authorized_keys`
    
3. **Ou crie usuários separados para cada um, com:**
    
    bash
    
    `sudo adduser nome sudo mkdir /home/nome/.ssh sudo nano /home/nome/.ssh/authorized_keys sudo chown -R nome:nome /home/nome/.ssh`
    
4. **Use grupos/sudo para controlar permissões.**