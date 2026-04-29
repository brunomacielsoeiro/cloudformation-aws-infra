# cloudformation-aws-infra


> 🎓 \*\*Contexto acadêmico\*\*
> Este repositório foi utilizado como atividade prática de estudos sobre
> Infraestrutura em Nuvem e Infraestrutura como Código (IaC) utilizando AWS
> CloudFormation, para fins educacionais e cumprimento de horas complementares.


## Decisões de Arquitetura - Amazon S3

- O template não fixa região da AWS, respeitando o princípio de reutilização
  entre múltiplas regiões.
- O nome do bucket é parametrizado para atender padrões de governança.
- Segurança é aplicada por padrão (criptografia, bloqueio de acesso público).
- Regras de lifecycle são utilizadas para controle de custos.
- Tags são definidas para fins de governança e rastreabilidade.
- O template prioriza reutilização e extensibilidade, podendo ser integrado a
  stacks maiores ou pipelines de infraestrutura.
  

# 🔐 Decisões de Arquitetura – Security Group

- O Security Group é criado de forma parametrizada para permitir reutilização
  em diferentes ambientes e VPCs.
- O acesso SSH é controlado por CIDR parametrizável, evitando exposição desnecessária.
- Portas HTTP e HTTPS são liberadas para acesso público, seguindo cenário comum
  de sistemas web.
- As regras de saída são explicitamente definidas para maior clareza e governança.
- Tags padronizadas são utilizadas para organização e rastreabilidade dos recursos.

