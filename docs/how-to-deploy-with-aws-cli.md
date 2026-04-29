# Guia pratico: Subindo infraestrutura com AWS CLI + CloudFormation

## Objetivo do guia

Este resumo descreve o fluxo basico para provisionar um bucket S3 e um Security Group utilizando AWS CLI em uma maquina desktop e CloudFormation.

---

## Pre-requisitos essenciais

- AWS CLI instalada no desktop
- Conta ativa na AWS
- Credenciais validas (IAM)
- Templates CloudFormation disponiveis localmente

---

## 1. Autenticar na AWS via AWS CLI (login web)

No terminal, execute:

aws login

O navegador sera aberto para autenticacao na AWS. Apos o login, confirme o acesso no AWS CLI.

---

## 2. Validar os templates CloudFormation

Validar o template do bucket S3:

aws cloudformation validate-template --template-body file://templates/s3-bucket.yaml

Validar o template do Security Group:

aws cloudformation validate-template --template-body file://templates/security-group.yaml

Se ambos forem validados sem erro, os templates estao prontos para uso.

---

## 3. Criar a stack do bucket S3

aws cloudformation create-stack --stack-name s3-estudos-cloudformation --template-body file://templates/s3-bucket.yaml --parameters ParameterKey=BucketName,ParameterValue=empresa-dev-estudos ParameterKey=Environment,ParameterValue=dev

---

## 4. Descobrir o ID da VPC

aws ec2 describe-vpcs

Localize e copie o valor do campo VpcId.

---

## 5. Criar a stack do Security Group

aws cloudformation create-stack --stack-name sg-estudos-cloudformation --template-body file://templates/security-group.yaml --parameters ParameterKey=VpcId,ParameterValue=vpc-0abc1234 ParameterKey=Environment,ParameterValue=dev ParameterKey=AllowedSshCidr,ParameterValue=0.0.0.0/0

Em ambientes produtivos, recomenda-se restringir o valor de AllowedSshCidr.

---

## Resultado esperado

Ao final do processo, a infraestrutura sera provisionada com sucesso via AWS CLI e CloudFormation, aplicando os conceitos de Infraestrutura como Codigo (IaC).
