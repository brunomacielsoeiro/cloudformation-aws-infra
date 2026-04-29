# cloudformation-aws-infra


> 🎓 \*\*Contexto acadêmico\*\*
> Este repositório foi utilizado como atividade prática de estudos sobre
> Infraestrutura em Nuvem e Infraestrutura como Código (IaC) utilizando AWS
> CloudFormation, para fins educacionais e cumprimento de horas complementares.


## Decisões de Arquitetura do bucket S3

- O template não fixa região da AWS, respeitando o princípio de reutilização
  entre múltiplas regiões.
- O nome do bucket é parametrizado para atender padrões de governança.
- Segurança é aplicada por padrão (criptografia, bloqueio de acesso público).
- Regras de lifecycle são utilizadas para controle de custos.
- Tags são definidas para fins de governança e rastreabilidade.

