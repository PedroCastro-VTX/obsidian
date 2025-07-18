1. **Pegar acessos servidores com Pai** 
	1. AWS 
	2. EC2
	3. PGAdmin
	4. Máquina Virtual Windows
	
2. **Verificar com Pai como vamos fazer a máquina virtual do linux. ==Opções:==**
	1. **Opção 2: Criar uma VM Linux com Docker na AWS (EC2)*
	
Para criar uma máquina virtual (VM) Linux na AWS, você precisa primeiro ==acessar o console de gerenciamento da AWS, selecionar o serviço EC2 (Elastic Compute Cloud, escolher uma imagem de máquina (AMI) Linux, criar uma instância e configurá-la conforme necessário==.
```
Passos Detalhados:

1. Acesso ao Console EC2: Acesse o console de gerenciamento da AWS e, em seguida, o serviço EC2. 

- Executar Instância: Clique em "Executar instância" para iniciar o processo de criação. 

- Escolha da Imagem (AMI):
    - Tipo de Instância: Selecione o tipo de instância (processador, memória, etc.) que você precisa. 

- AMI: Escolha a imagem de máquina desejada (por exemplo, Amazon Linux, Ubuntu, etc.). 

- Par de Chaves: Crie ou selecione um par de chaves para acesso SSH à sua instância. 

- **Configuração da Rede:**
    - **Grupo de Segurança:** Selecione ou crie um grupo de segurança para controlar o tráfego de entrada e saída da instância. 

- **VPC (Virtual Private Cloud):** Se você tiver uma VPC, escolha-a aqui. 

- **Configuração de Armazenamento:**
    - **Volume:** Selecione o tamanho e o tipo de volume de armazenamento desejado (ex: SSD, HDD). 

- Configuração Avançada:
    - Etiquetas:** Adicione etiquetas para identificar a instância. 

- **Configuração do Usuário (User Data):** Você pode adicionar scripts para configuração inicial, caso necessário. 

- Execução:
    - Executar Instância:** Clique em "Executar instância" para criar a instância. 

- Acesso:
    - SSH: Use um cliente SSH (como PuTTY ou o terminal baseado em navegador do Lightsail) para se conectar à instância. 

- Informações de Acesso: Use o IP público ou o endereço DNS da instância para se conectar.


```

3. **Configurar máquina virtual**

4. **Instalar itens na máquina virtual:** 
	1. Docker ( Nas 4 máquinas Virtuais )
	2. Servidores ( 2 máquinas virtual )
		1. Limitar Acessos para certos servidores
		2. S3 - Servidores internos
	3. PgAdmin ( 1 máquina virtual )
	4. Navegador ( Nas 4 máquinas Virtuais )
	5. Nginx ( 1 máquina virtual )
	6. Grafana Cloud ( Nas 4 máquinas Virtuais )
	7. Terraform ( Nas 4 máquinas Virtuais )
	
5. **Configurar aplicativos acima com base em seus respectivos docs:**
	1. [[Grafana]] Cloud
	2. [[Docker]]
	3. [[Nginx]]
	4. [[Linux Básico]]
	5. [[PG Admin]]
	6. [[EC2 Servidores]]
	

[[Passos a passos Criação EC2 e Servidores]]

