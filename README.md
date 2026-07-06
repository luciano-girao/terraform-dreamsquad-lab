# terraform-dreamsquad-lab

> Laboratório de Terraform com foco em infraestrutura como código (IaC) para provisionamento de recursos AWS. Desenvolvido como projeto de estudo e portfólio para a DreamSquad.

![Terraform](https://img.shields.io/badge/Terraform-623CE4?style=for-the-badge&logo=terraform&logoColor=white)
![AWS](https://img.shields.io/badge/AWS-232F3E?style=for-the-badge&logo=amazon-aws&logoColor=white)
![HCL](https://img.shields.io/badge/HCL-844FBA?style=for-the-badge&logo=terraform&logoColor=white)
![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)

---

## 📌 Sobre o projeto

Este repositório demonstra o uso prático do **Terraform** para provisionar e gerenciar infraestrutura na AWS de forma organizada, reutilizável e versionada. O projeto simula um ambiente de laboratório real com boas práticas de IaC: modularização, variáveis, outputs e estado remoto.

Contexto: desenvolvido durante processo seletivo/hackathon DreamSquad como demonstração de conhecimento em Cloud & DevOps.

---

## 📂 Estrutura do repositório

```
terraform-dreamsquad-lab/
├── main.tf                  # Recursos principais (EC2, VPC, SG)
├── variables.tf             # Declaração de todas as variáveis
├── outputs.tf               # Outputs do provisionamento
├── provider.tf              # Configuração do provider AWS
├── terraform.tfvars         # Valores das variáveis (não versionado em produção)
├── modules/
│   ├── ec2/                   # Módulo para instâncias EC2
│   ├── vpc/                   # Módulo para rede VPC
│   └── security-group/        # Módulo para Security Groups
└── README.md
```

---

## 🚀 Recursos provisionados

| Recurso | Descrição |
|---|---|
| `aws_vpc` | VPC dedicada com CIDR customizável |
| `aws_subnet` | Subnet pública para o laboratório |
| `aws_internet_gateway` | IGW para acesso à internet |
| `aws_route_table` | Tabela de rotas associada à subnet |
| `aws_security_group` | SG com regras de SSH e HTTP |
| `aws_instance` | Instância EC2 Amazon Linux 2 (t2.micro) |
| `aws_key_pair` | Par de chaves para acesso SSH |

---

## 📝 Exemplos de código

### provider.tf
```hcl
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 5.0"
    }
  }
}

provider "aws" {
  region = var.aws_region
}
```

### variables.tf
```hcl
variable "aws_region" {
  description = "Região AWS onde os recursos serão provisionados"
  type        = string
  default     = "us-east-1"
}

variable "instance_type" {
  description = "Tipo da instância EC2"
  type        = string
  default     = "t2.micro"
}

variable "project_name" {
  description = "Nome do projeto para tags dos recursos"
  type        = string
  default     = "dreamsquad-lab"
}
```

### main.tf (trecho)
```hcl
resource "aws_vpc" "main" {
  cidr_block           = "10.0.0.0/16"
  enable_dns_hostnames = true
  enable_dns_support   = true

  tags = {
    Name    = "${var.project_name}-vpc"
    Project = var.project_name
  }
}

resource "aws_instance" "lab" {
  ami                    = data.aws_ami.amazon_linux.id
  instance_type          = var.instance_type
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.lab_sg.id]

  tags = {
    Name    = "${var.project_name}-ec2"
    Project = var.project_name
  }
}
```

### outputs.tf
```hcl
output "instance_public_ip" {
  description = "IP público da instância EC2"
  value       = aws_instance.lab.public_ip
}

output "vpc_id" {
  description = "ID da VPC criada"
  value       = aws_vpc.main.id
}
```

---

## 🛠️ Como usar

### Pré-requisitos
- [Terraform](https://developer.hashicorp.com/terraform/downloads) v1.0+
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-cliv2.html) configurado
- Credenciais AWS com permissões de EC2, VPC e IAM

### Passos

```bash
# 1. Clone o repositório
git clone https://github.com/luciano-girao/terraform-dreamsquad-lab.git
cd terraform-dreamsquad-lab

# 2. Inicialize o Terraform (baixa providers)
terraform init

# 3. Visualize o plano de execução
terraform plan

# 4. Aplique a infraestrutura
terraform apply

# 5. Ao finalizar os estudos, destrua os recursos
terraform destroy
```

> ⚠️ **Atenção:** recursos AWS podem gerar custos. Use `terraform destroy` após os estudos.

---

## 📚 O que aprendi com esse projeto

- Escrita de código HCL para provisionamento de infraestrutura AWS
- Organização de projetos Terraform com arquivos separados por responsabilidade
- Uso de variáveis, outputs e data sources no Terraform
- Modularização de recursos para reutilização
- Boas práticas de IaC: código legível, tagueamento e descrições
- Fluxo completo: `init` → `plan` → `apply` → `destroy`

---

## 👤 Autor

**Luciano Henrique Morais Girão**  
[LinkedIn](https://www.linkedin.com/in/lucianogirao) • [GitHub](https://github.com/luciano-girao)
