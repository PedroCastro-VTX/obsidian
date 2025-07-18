
[Conectar-se a uma instância do Amazon EC2 via portas HTTP ou HTTPS \| AWS re:Post](https://repost.aws/pt/knowledge-center/connect-http-https-ec2)
[COMO CRIAR E VALIDAR UM CERTIFICADO HTTPS NA AWS - YouTube](https://www.youtube.com/watch?v=1NK5m28sR-Q&ab_channel=CloudTreinamentos)

Para habilitar HTTPS em uma instância EC2, ==é necessário configurar um certificado SSL/TLS, abrir a porta 443 e configurar o balanceador de carga (se estiver em uso) para redirecionar o tráfego HTTP para HTTPS==.

Passos:

1. **1.** **Certificado SSL/TLS:**
    
    - Gerar uma solicitação de assinatura de certificado (CSR) na instância EC2. 
    - Solicitar o certificado a uma autoridade certificadora (CA), como a Let's Encrypt. 
    - Instalar o certificado e a chave privada na instância EC2. 
    
2. **2.** **Grupo de Segurança:**
    
    - Acessar o console do Amazon EC2. 
    - Selecionar o grupo de segurança associado à instância. 
    - Adicionar uma regra de entrada para permitir o tráfego na porta 443 (HTTPS). 
    - Configurar a regra para permitir o tráfego de qualquer endereço IP ou um intervalo específico. 
    
3. **3.** **Balanceador de Carga (se aplicável):**
    
    - Configurar o balanceador de carga (Application Load Balancer ou Classic Load Balancer). 
    - Criar ou modificar um listener para HTTPS na porta 443. 
    - Configurar o certificado SSL/TLS no listener. 
    - Criar uma regra para redirecionar o tráfego HTTP (porta 80) para HTTPS (porta 443). 
    
4. **4.** **Verificação:**
    
    - Acessar o site utilizando o protocolo HTTPS no navegador. 
    - Verificar se o indicador de segurança (cadeado) está visível e se o certificado é válido. 
    

Observações:

- Certifique-se de que o servidor web (ex: Apache, Nginx, IIS) está configurado para utilizar o certificado SSL/TLS e atender às requisições na porta 443. 
- Se estiver utilizando um balanceador de carga, ele pode encapsular o tráfego HTTPS, redirecionando para uma porta diferente no servidor web. 
- Considere a automação da renovação do certificado SSL/TLS para evitar interrupções no serviço. 
- Para instâncias Windows EC2, é possível instalar o certificado via IIS.
