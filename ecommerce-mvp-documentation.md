# Documentação do Projeto: MVP de E-commerce na AWS

## Índice
1. [Visão Geral](#visão-geral)
2. [Pré-requisitos](#pré-requisitos)
3. [Provisionamento da Infraestrutura](#provisionamento-da-infraestrutura)
   - [Configuração Inicial](#configuração-inicial)
   - [Execução do Terraform](#execução-do-terraform)
4. [Configuração do Ambiente](#configuração-do-ambiente)
   - [Preparação do Ambiente Ansible](#preparação-do-ambiente-ansible)
   - [Execução do Playbook Ansible](#execução-do-playbook-ansible)
5. [Validação e Testes](#validação-e-testes)
6. [Monitoramento e Manutenção](#monitoramento-e-manutenção)
7. [Considerações de Segurança](#considerações-de-segurança)
8. [Próximos Passos](#próximos-passos)

## Visão Geral

Este projeto visa criar um MVP (Produto Mínimo Viável) de uma plataforma de e-commerce na AWS, utilizando a arquitetura apresentada. A solução combina Terraform para provisionamento de infraestrutura e Ansible para configuração de software, com o objetivo de fornecer uma implementação ágil e automatizada.

A arquitetura inclui os seguintes componentes principais:

- **VPC e Subnet**: Rede virtual com uma subnet pública.
- **EC2 Instance**: Máquina virtual para hospedar a aplicação Magento.
- **Security Group**: Regras de firewall para a instância EC2.
- **Magento**: Plataforma de e-commerce de código aberto.
- **PHP, MySQL, Redis**: Tecnologias de back-end e cache.
- **Nginx**: Servidor web para a aplicação Magento.

## Pré-requisitos

1. **AWS CLI**: Ter o AWS CLI instalado e configurado com suas credenciais.
2. **Terraform**: Ter o Terraform instalado (versão 1.0.0 ou superior).
3. **Ansible**: Ter o Ansible instalado (versão 2.9 ou superior).
4. **Chave SSH**: Ter uma chave SSH configurada na AWS.
5. **Senhas**: Definir as seguintes senhas de ambiente:
   - `MYSQL_ROOT_PASSWORD`: Senha do usuário root do MySQL.
   - `MAGENTO_DB_PASSWORD`: Senha do usuário do banco de dados Magento.

## Provisionamento da Infraestrutura

### Configuração Inicial

1. Crie o arquivo `terraform.tfvars` com as seguintes variáveis:
```hcl
aws_region   = "us-east-1"
project_name = "magento-mvp"
vpc_cidr     = "10.0.0.0/16"
subnet_cidr  = "10.0.1.0/24"
admin_ip     = "your_ip_address" # Endereço IP do administrador para acesso SSH
key_name     = "your_ssh_key_name"
```

2. Inicialize o Terraform:
```bash
terraform init
```

### Execução do Terraform

1. Visualize o plano de execução:
```bash
terraform plan -out=tfplan
```

2. Aplique o plano:
```bash
terraform apply tfplan
```

O Terraform irá:
- Criar a VPC e a subnet pública
- Configurar o Security Group
- Provisionar a instância EC2 com a AMI especificada

## Configuração do Ambiente

### Preparação do Ambiente Ansible

1. Acesse a instância EC2 criada pelo Terraform:
```bash
ssh -i your_key.pem ubuntu@public_ip_address
```

2. Instale o Ansible na instância:
```bash
sudo apt update
sudo apt install -y software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install -y ansible
```

3. Copie o inventário Ansible gerado pelo Terraform para o diretório `ansible/`:
```bash
cp inventory.ini ../ansible/
```

### Execução do Playbook Ansible

1. Defina as variáveis de ambiente necessárias:
```bash
export MYSQL_ROOT_PASSWORD="sua_senha_root"
export MAGENTO_DB_PASSWORD="sua_senha_magento"
```

2. Execute o playbook Ansible:
```bash
cd ansible
ansible-playbook -i inventory.ini playbook.yml
```

O Ansible irá:
- Instalar os pacotes necessários (Nginx, PHP, MySQL, Redis)
- Configurar o MySQL com o usuário e banco de dados da Magento
- Configurar o Redis com limites de memória
- Baixar e instalar a plataforma Magento
- Configurar as permissões e o Nginx para a Magento

## Validação e Testes

1. Acesse a aplicação Magento no endereço IP público da instância EC2.
2. Verifique se a instalação foi concluída com sucesso.
3. Teste a funcionalidade básica da plataforma de e-commerce.

## Monitoramento e Manutenção

1. Monitore a instância EC2 no Console da AWS.
2. Verifique os logs da aplicação Magento, Nginx e outros serviços.
3. Mantenha o sistema atualizado com as versões mais recentes do software.

## Considerações de Segurança

1. **Acesso Restrito**: O Security Group permite acesso SSH apenas do IP do administrador e acesso HTTP/HTTPS de qualquer endereço IP.
2. **Senhas Seguras**: As senhas do MySQL e Magento são definidas como variáveis de ambiente.
3. **Atualização de Segurança**: Mantenha o sistema operacional, Magento e demais softwares atualizados.
4. **Backup e Recuperação**: Implemente soluções de backup e recuperação de dados.

## Próximos Passos

1. **Integração Contínua**: Automatizar o processo de implantação com pipelines CI/CD.
2. **Balanceamento de Carga**: Adicionar um balanceador de carga (ELB) para escalabilidade.
3. **Monitoramento Avançado**: Integrar soluções de monitoramento (CloudWatch, X-Ray, etc.).
4. **Segurança Avançada**: Implementar proteção contra ataques (WAF, GuardDuty, etc.).
5. **Otimização de Custos**: Analisar e ajustar o dimensionamento dos recursos.
