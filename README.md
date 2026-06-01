# ☁️ CloudFormation AWS Infrastructure

> Projeto de estudos sobre Infraestrutura como Código (IaC) utilizando AWS CloudFormation, abordando provisionamento declarativo de S3 e Security Groups com boas práticas de segurança e governança. Atividade acadêmica para cumprimento de horas complementares.

---

## 📋 Índice

- [Sobre o Projeto](#sobre-o-projeto)
- [Arquitetura](#arquitetura)
- [Estrutura do Projeto](#estrutura-do-projeto)
- [Templates](#templates)
- [Como Usar](#como-usar)
- [Decisões de Arquitetura](#decisões-de-arquitetura)
- [Boas Práticas Aplicadas](#boas-práticas-aplicadas)
- [Conexão com outros projetos](#conexão-com-outros-projetos)
- [Referências](#referências)

---

## Sobre o Projeto

Este repositório demonstra o provisionamento de infraestrutura AWS utilizando **CloudFormation** — o serviço nativo da AWS para IaC. Os templates criam recursos seguindo boas práticas de segurança, governança e organização.

### O que este projeto demonstra

- Templates YAML declarativos e parametrizados
- S3 com criptografia, versionamento e lifecycle
- Security Groups com regras controladas
- Deploy via AWS CLI
- Tags padronizadas para governança

---

## Arquitetura

```
┌─────────────────────────────────────────────────────────────┐
│                    AWS CloudFormation                         │
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  Stack: s3-estudos-cloudformation                        ││
│  │                                                          ││
│  │  ┌────────────────────────────────────────────────────┐ ││
│  │  │  S3 Bucket                                          │ ││
│  │  │  • Versionamento habilitado                         │ ││
│  │  │  • Criptografia AES-256                             │ ││
│  │  │  • Public Access Block (total)                      │ ││
│  │  │  • Lifecycle: 90d → Glacier, 365d → Delete          │ ││
│  │  └────────────────────────────────────────────────────┘ ││
│  └─────────────────────────────────────────────────────────┘│
│                                                              │
│  ┌─────────────────────────────────────────────────────────┐│
│  │  Stack: sg-estudos-cloudformation                        ││
│  │                                                          ││
│  │  ┌────────────────────────────────────────────────────┐ ││
│  │  │  Security Group                                     │ ││
│  │  │  • Inbound: SSH (22) — CIDR parametrizável          │ ││
│  │  │  • Inbound: HTTP (80) — público                     │ ││
│  │  │  • Inbound: HTTPS (443) — público                   │ ││
│  │  │  • Outbound: All — liberado                         │ ││
│  │  └────────────────────────────────────────────────────┘ ││
│  └─────────────────────────────────────────────────────────┘│
│                                                              │
└─────────────────────────────────────────────────────────────┘
```

---

## Estrutura do Projeto

```
cloudformation-aws-infra/
├── README.md                              ← Este arquivo
├── notes-cloudformation.md                ← Anotações sobre CloudFormation
├── docs/
│   └── how-to-deploy-with-aws-cli.md      ← Guia prático de deploy
└── templates/
    ├── s3-bucket.yaml                     ← Template S3 com segurança
    └── security-group.yaml                ← Template Security Group
```

---

## Templates

### S3 Bucket (`templates/s3-bucket.yaml`)

| Recurso | Configuração |
|---------|-------------|
| Versionamento | ✅ Habilitado |
| Criptografia | AES-256 (SSE-S3) |
| Public Access Block | ✅ Total (4 flags) |
| Lifecycle — Glacier | 90 dias |
| Lifecycle — Expiração | 365 dias |
| Tags | Name, Environment, Project, ManagedBy |

**Parâmetros:**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `BucketName` | String | Nome único global do bucket |
| `Environment` | String | `dev`, `hom` ou `prod` |

### Security Group (`templates/security-group.yaml`)

| Regra | Porta | Origem | Descrição |
|-------|-------|--------|-----------|
| SSH | 22 | CIDR parametrizável | Acesso controlado |
| HTTP | 80 | 0.0.0.0/0 | Tráfego web |
| HTTPS | 443 | 0.0.0.0/0 | Tráfego web seguro |
| Outbound | All | 0.0.0.0/0 | Saída liberada |

**Parâmetros:**

| Parâmetro | Tipo | Descrição |
|-----------|------|-----------|
| `VpcId` | AWS::EC2::VPC::Id | VPC onde criar o SG |
| `Environment` | String | `dev`, `hom` ou `prod` |
| `AllowedSshCidr` | String | CIDR para SSH (default: 0.0.0.0/0) |

---

## Como Usar

### Pré-requisitos

- AWS CLI v2 instalada e configurada
- Conta AWS com permissões para CloudFormation, S3, EC2

### Deploy rápido

```bash
# 1. Validar templates
aws cloudformation validate-template \
  --template-body file://templates/s3-bucket.yaml

aws cloudformation validate-template \
  --template-body file://templates/security-group.yaml

# 2. Criar stack S3
aws cloudformation create-stack \
  --stack-name s3-estudos \
  --template-body file://templates/s3-bucket.yaml \
  --parameters \
    ParameterKey=BucketName,ParameterValue=meu-bucket-dev-estudos \
    ParameterKey=Environment,ParameterValue=dev

# 3. Criar stack Security Group
aws cloudformation create-stack \
  --stack-name sg-estudos \
  --template-body file://templates/security-group.yaml \
  --parameters \
    ParameterKey=VpcId,ParameterValue=vpc-0abc1234 \
    ParameterKey=Environment,ParameterValue=dev

# 4. Verificar status
aws cloudformation describe-stacks --stack-name s3-estudos
aws cloudformation describe-stacks --stack-name sg-estudos
```

Guia completo: [docs/how-to-deploy-with-aws-cli.md](./docs/how-to-deploy-with-aws-cli.md)

---

## Decisões de Arquitetura

### S3 Bucket

| Decisão | Justificativa |
|---------|---------------|
| Sem região fixa no template | Reutilização entre regiões |
| Nome parametrizado | Atende padrões de governança |
| Criptografia por padrão | Segurança desde o início |
| Public Access Block total | Previne exposição acidental |
| Lifecycle rules | Controle de custos automático |
| Tags padronizadas | Rastreabilidade e governança |

### Security Group

| Decisão | Justificativa |
|---------|---------------|
| SSH com CIDR parametrizável | Flexível por ambiente |
| HTTP/HTTPS públicos | Cenário web padrão |
| Egress explícito | Clareza e governança |
| Tags com ambiente | Organização multi-ambiente |

---

## Boas Práticas Aplicadas

| Prática | Como |
|---------|------|
| Parametrização | Todos os valores configuráveis via Parameters |
| Validação | `AllowedValues` para Environment |
| Segurança por padrão | Criptografia + Public Access Block |
| Outputs úteis | ARN, IDs para integração com outras stacks |
| Descrições | Todos os parâmetros e outputs documentados |
| Tags | Padronizadas em todos os recursos |

---

## Conexão com outros projetos

| Projeto | Relação |
|---------|---------|
| `terraform-aws-infra` | Mesma infra, ferramenta diferente (comparação IaC) |
| `iac-cloudfront-s3` | CloudFormation avançado (CloudFront + S3) |
| `networking-cloud-fundamentos` | Conceitos de VPC e Security Groups |
| `seguranca-cloud-iam` | IAM policies para acesso aos recursos |

---

## Referências

- [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/)
- [CloudFormation Resource Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-template-resource-type-ref.html)
- [S3 Best Practices](https://docs.aws.amazon.com/AmazonS3/latest/userguide/security-best-practices.html)
- [CloudFormation Best Practices](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/best-practices.html)

---

## 📄 Licença

MIT License
