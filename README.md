# 🚀 Implementando sua Primeira Stack com AWS CloudFormation

Este repositório documenta minha jornada de aprendizado e implementação prática de Infrastructure as Code (IaC) usando AWS CloudFormation, desenvolvido como parte do desafio da DIO.

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Arquitetura Implementada](#arquitetura-implementada)
- [Recursos Criados](#recursos-criados)
- [Templates CloudFormation](#templates-cloudformation)
- [Como Executar](#como-executar)
- [Conceitos Aprendidos](#conceitos-aprendidos)
- [Troubleshooting](#troubleshooting)
- [Recursos Úteis](#recursos-úteis)

## 🎯 Sobre o Projeto

Este projeto implementa uma infraestrutura web básica na AWS usando CloudFormation, demonstrando os principais conceitos de IaC:

- **Versionamento**: Templates versionados e organizados
- **Reutilização**: Parâmetros e outputs para flexibilidade
- **Boas Práticas**: Estrutura modular e documentação clara
- **Segurança**: Security Groups com regras específicas

## 🏗️ Arquitetura Implementada

```
┌─────────────────────────────────────────────┐
│                   VPC                       │
│  ┌─────────────────────────────────────────┐ │
│  │            Public Subnet               │ │
│  │  ┌─────────────────┐  ┌───────────────┐ │ │
│  │  │    EC2 Web      │  │   Security    │ │ │
│  │  │    Server       │  │   Group       │ │ │
│  │  │  (Apache)       │  │  (HTTP/SSH)   │ │ │
│  │  └─────────────────┘  └───────────────┘ │ │
│  └─────────────────────────────────────────┘ │
│  ┌─────────────────────────────────────────┐ │
│  │           Internet Gateway             │ │
│  └─────────────────────────────────────────┘ │
└─────────────────────────────────────────────┘
```

## 📦 Recursos Criados

### Rede e Conectividade
- **VPC** (Virtual Private Cloud)
- **Subnet Pública**
- **Internet Gateway**
- **Route Table** com rotas para internet

### Computação
- **Instância EC2** (t2.micro - Free Tier)
- **Security Group** com regras HTTP (80) e SSH (22)
- **Key Pair** para acesso SSH

### Automação
- **User Data Script** para configuração automática do servidor web

## 📄 Templates CloudFormation

### Template Principal: `web-infrastructure.yaml`

Template principal que cria toda a infraestrutura necessária para hospedar uma aplicação web simples.

**Parâmetros Configuráveis:**
- Nome da Key Pair
- Tipo de instância EC2
- CIDR da VPC
- Nome do projeto

**Outputs Gerados:**
- URL público da aplicação
- IP público da instância
- ID dos recursos criados

### Template Modular: `security-groups.yaml`

Template focado apenas na criação de Security Groups, demonstrando modularidade.

## 🚀 Como Executar

### Pré-requisitos

1. Conta AWS ativa
2. AWS CLI configurado ou acesso ao Console AWS
3. Key Pair EC2 criada na região desejada

### Deploy via AWS CLI

```bash
# 1. Clone o repositório
git clone https://github.com/seu-usuario/aws-cloudformation-first-stack.git
cd aws-cloudformation-first-stack

# 2. Fazer deploy da stack
aws cloudformation create-stack \
  --stack-name minha-primeira-stack \
  --template-body file://templates/web-infrastructure.yaml \
  --parameters ParameterKey=KeyPairName,ParameterValue=sua-key-pair \
  --capabilities CAPABILITY_IAM \
  --region us-east-1

# 3. Monitorar o status
aws cloudformation describe-stacks \
  --stack-name minha-primeira-stack \
  --query 'Stacks[0].StackStatus'

# 4. Obter outputs
aws cloudformation describe-stacks \
  --stack-name minha-primeira-stack \
  --query 'Stacks[0].Outputs'
```

### Deploy via Console AWS

1. Acesse o console AWS CloudFormation
2. Clique em "Create Stack" > "With new resources"
3. Faça upload do arquivo `web-infrastructure.yaml`
4. Preencha os parâmetros solicitados
5. Revise e confirme a criação

### Testando a Aplicação

Após o deploy bem-sucedido:

1. Acesse o output `WebsiteURL` gerado pela stack
2. Verifique se a página web Apache está funcionando
3. Teste conectividade SSH (opcional)

```bash
# SSH para a instância (substitua pelos valores reais)
ssh -i sua-key-pair.pem ec2-user@IP_PUBLICO_DA_INSTANCIA
```

## 📚 Conceitos Aprendidos

### Infrastructure as Code (IaC)

**Antes do CloudFormation:**
- Configuração manual via console
- Inconsistências entre ambientes
- Dificuldade de versionamento
- Processo sujeito a erros

**Depois do CloudFormation:**
- Infraestrutura versionada em código
- Deployments consistentes e repetíveis
- Rollback automático em caso de erro
- Documentação integrada

### Principais Recursos CloudFormation

#### 1. **Parameters**
```yaml
Parameters:
  KeyPairName:
    Type: AWS::EC2::KeyPair::KeyName
    Description: Nome da Key Pair para acesso SSH
```

#### 2. **Resources**
```yaml
Resources:
  WebServerInstance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !Ref LatestAmiId
      InstanceType: !Ref InstanceType
```

#### 3. **Outputs**
```yaml
Outputs:
  WebsiteURL:
    Description: URL da aplicação web
    Value: !Sub 'http://${WebServerInstance.PublicDnsName}'
```

#### 4. **Intrinsic Functions**
- `!Ref`: Referencia outros recursos
- `!Sub`: Substituição de variáveis
- `!GetAtt`: Obtém atributos de recursos
- `!Join`: Concatena valores

### Boas Práticas Implementadas

✅ **Nomenclatura Consistente**
- Todos os recursos seguem padrão de nomenclatura
- Tags aplicadas para organização

✅ **Segurança**
- Security Groups com regras mínimas necessárias
- Uso de latest AMI automaticamente

✅ **Modularidade**
- Templates separados por responsabilidade
- Parâmetros para flexibilidade

✅ **Documentação**
- Descrições em todos os recursos
- README detalhado

## 🔧 Troubleshooting

### Problemas Comuns

#### ❌ Erro: "Key Pair não existe"
**Solução:** Criar key pair antes do deploy
```bash
aws ec2 create-key-pair \
  --key-name minha-key \
  --query 'KeyMaterial' \
  --output text > minha-key.pem
chmod 400 minha-key.pem
```

#### ❌ Erro: "Insufficient capacity"
**Solução:** Tentar região diferente ou tipo de instância diferente

#### ❌ Stack travada em "CREATE_IN_PROGRESS"
**Solução:** Verificar logs do CloudFormation no console AWS

### Comandos Úteis

```bash
# Listar todas as stacks
aws cloudformation list-stacks

# Ver eventos da stack
aws cloudformation describe-stack-events \
  --stack-name minha-primeira-stack

# Deletar stack
aws cloudformation delete-stack \
  --stack-name minha-primeira-stack

# Validar template
aws cloudformation validate-template \
  --template-body file://templates/web-infrastructure.yaml
```

## 💰 Custos

Recursos utilizados no Free Tier:
- **EC2 t2.micro**: 750 horas/mês gratuitas
- **VPC, Subnets, IGW**: Sem custo
- **Security Groups**: Sem custo

⚠️ **Atenção**: Lembre-se de deletar a stack após os testes para evitar custos desnecessários.

## 🎓 Próximos Passos

Para expandir este projeto, considere:

1. **Multi-AZ Deployment**: Distribuir recursos em múltiplas zonas
2. **Load Balancer**: Implementar ALB para alta disponibilidade
3. **Auto Scaling**: Configurar scaling automático
4. **RDS**: Adicionar banco de dados
5. **CloudWatch**: Implementar monitoramento e alertas
6. **CI/CD**: Integrar com pipeline de deploy

## 📖 Recursos Úteis

### Documentação Oficial
- [AWS CloudFormation Documentation](https://docs.aws.amazon.com/cloudformation/)
- [CloudFormation Template Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-reference.html)
- [AWS CLI CloudFormation Commands](https://docs.aws.amazon.com/cli/latest/reference/cloudformation/)

### Ferramentas Úteis
- [CloudFormation Designer](https://console.aws.amazon.com/cloudformation/designer/) - Editor visual
- [cfn-lint](https://github.com/aws-cloudformation/cfn-lint) - Validação de templates
- [AWS Cloud9](https://aws.amazon.com/cloud9/) - IDE na nuvem

### Comunidade
- [AWS CloudFormation GitHub](https://github.com/aws-cloudformation/)
- [Stack Overflow - CloudFormation](https://stackoverflow.com/questions/tagged/amazon-cloudformation)

## 🏆 Conclusão

Este projeto demonstra a implementação prática de Infrastructure as Code usando AWS CloudFormation. Através desta experiência, pude consolidar conhecimentos sobre:

- Automação de infraestrutura
- Versionamento de recursos
- Boas práticas de segurança
- Documentação técnica

O CloudFormation oferece uma forma poderosa e confiável de gerenciar infraestrutura na AWS, proporcionando consistência, versionamento e facilidade de manutenção.

--- 
**Desafio:** DIO - Implementando sua Primeira Stack com AWS CloudFormation
