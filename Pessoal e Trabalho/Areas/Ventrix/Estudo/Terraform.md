#estudo #ventrix

Terraform é uma ferramenta de código aberto para provisionamento de infraestrutura como código (IaC).

- **Linguagem:** usa HCL (HashiCorp Configuration Language), que é declarativa.
- **Funcionamento:** você define os recursos (como servidores, redes, bancos de dados) em arquivos `.tf` e o Terraform interage com os provedores (AWS, Azure, etc.) para criá-los.
- **Comandos principais:**
    - `terraform init` (inicializa o projeto)
    - `terraform plan` (mostra o que será feito)
    - `terraform apply` (executa as mudanças)
- **Vantagem:** permite controle de versão da infraestrutura, automação e repetibilidade com segurança.

### 1. Como começar com Terraform**

**Passos iniciais:**

1. Instale o Terraform:  
    https://developer.hashicorp.com/terraform/downloads
    
2. Crie um diretório e um arquivo `main.tf` com algo simples, como:
    
    hcl
    `provider "aws" {   region = "us-east-1" }  resource "aws_s3_bucket" "meu_bucket" {   bucket = "meu-nome-de-bucket-unico"   acl    = "private" }`
    
3. Inicie o projeto:
    
    bash
    `terraform init terraform plan terraform apply`
    
4. Explore o Terraform Cloud (opcional) e variáveis, módulos, estados remotos etc.
    
5. Documentação: [Terraform Docs](https://developer.hashicorp.com/terraform/docs)