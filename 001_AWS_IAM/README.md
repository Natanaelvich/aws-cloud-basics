# AWS IAM - Identity and Access Management

## O que é AWS IAM?

AWS IAM (Identity and Access Management) é um serviço da AWS que permite gerenciar de forma segura o acesso aos recursos e serviços da AWS. O IAM é fundamental para manter a segurança da sua infraestrutura na nuvem.

## Conceitos Principais

### 1. **Usuários (Users)**
Usuários representam pessoas ou aplicações que precisam acessar recursos AWS. Cada usuário tem:
- Um nome único
- Credenciais (senha e/ou chaves de acesso)
- Permissões definidas através de políticas

### 2. **Grupos (Groups)**
Grupos são coleções de usuários. Você pode:
- Organizar usuários por função ou departamento
- Aplicar políticas a grupos em vez de usuários individuais
- Facilitar o gerenciamento de permissões em larga escala

### 3. **Roles (Funções)**
Roles são identidades que você cria com permissões específicas e que podem ser assumidas por:
- Usuários da sua conta AWS
- Serviços AWS (como Lambda, EC2)
- Identidades externas (usuários de outras contas AWS ou serviços federados)

### 4. **Políticas (Policies)**
Políticas são documentos JSON que definem permissões. Elas especificam:
- **Effect**: Permitir (Allow) ou Negar (Deny)
- **Action**: Ações que podem ser executadas (ex: `s3:GetObject`, `ec2:StartInstances`)
- **Resource**: Recursos específicos onde a ação pode ser executada
- **Condition**: Condições opcionais para aplicar a política

**Exemplo de Policy:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::meu-bucket/*"
    }
  ]
}
```

## Princípios de Segurança

### 1. **Princípio do Menor Privilégio (Least Privilege Principle)**
Conceda apenas as permissões mínimas necessárias para que usuários, grupos ou roles realizem suas tarefas. Evite políticas muito permissivas como `*:*`.

### 2. **Separação de Responsabilidades**
Diferentes usuários devem ter diferentes níveis de acesso baseados em suas responsabilidades.

### 3. **Rotação de Credenciais**
Regularmente, rotacione chaves de acesso e senhas para reduzir o risco de comprometimento.

### 4. **MFA (Multi-Factor Authentication)**
Habilite autenticação de múltiplos fatores para contas administrativas, adicionando uma camada extra de segurança.

## Tipos de Políticas

### **Políticas Gerenciadas pela AWS**
- Pré-configuradas pela AWS
- Exemplos: `AmazonS3ReadOnlyAccess`, `PowerUserAccess`
- Facilmente anexáveis a usuários, grupos ou roles

### **Políticas Gerenciadas pelo Cliente**
- Criadas e gerenciadas por você
- Permitem controle total sobre as permissões
- Reutilizáveis em múltiplos usuários/grupos/roles

### **Políticas Inline**
- Anexadas diretamente a um usuário, grupo ou role
- Não podem ser reutilizadas
- Úteis para permissões específicas

## Recursos Seguros por Padrão

Por padrão, recursos na AWS são **privados**:
- Nenhum acesso é concedido até que explicitamente permitido
- Apenas o proprietário da conta root tem acesso inicial
- Usuários novos não têm permissões até receberem políticas

## Melhores Práticas

1. **Não use a conta root** para operações diárias
2. **Use roles** para serviços AWS em vez de chaves de acesso
3. **Habilite CloudTrail** para auditoria de ações IAM
4. **Revise permissões regularmente** e remova acesso desnecessário
5. **Use grupos** para gerenciar permissões em vez de aplicar diretamente aos usuários
6. **Teste políticas** em ambiente de desenvolvimento antes de produção
7. **Documente políticas customizadas** explicando seu propósito

## Casos de Uso Comuns

- **Desenvolvedores**: Acesso para deploy de aplicações em serviços específicos
- **DevOps**: Permissões para gerenciar infraestrutura (EC2, ECS, Lambda)
- **Analistas**: Acesso somente leitura para análise de logs e métricas
- **Aplicações**: Roles para serviços Lambda acessarem S3, DynamoDB, etc.

## Importância para Certificação AWS

O IAM é fundamental para o exame AWS Certified Developer – Associate, pois:
- Todos os serviços AWS integram com IAM
- Questões sobre segurança e acesso são frequentes
- É essencial entender como políticas funcionam para trabalhar com outros serviços

## Próximos Passos

Após entender os conceitos básicos, pratique criando:
- Usuários e grupos
- Políticas customizadas
- Roles para serviços AWS
- Teste de permissões e troubleshooting

---

**Recursos Oficiais:**
- [Documentação AWS IAM](https://docs.aws.amazon.com/iam/)
- [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

