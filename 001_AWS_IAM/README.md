# AWS IAM - Identity and Access Management

## Índice

- [O que é AWS IAM?](#o-que-é-aws-iam)
- [Conceitos Principais](#conceitos-principais)
  - [1. Usuários (Users)](#1-usuários-users)
  - [2. Grupos (Groups)](#2-grupos-groups)
  - [3. Roles (Funções)](#3-roles-funções)
  - [4. Políticas (Policies)](#4-políticas-policies)
- [Princípios de Segurança](#princípios-de-segurança)
- [Tipos de Políticas](#tipos-de-políticas)
  - [Políticas Gerenciadas pela AWS](#políticas-gerenciadas-pela-aws)
  - [Políticas Gerenciadas pelo Cliente](#políticas-gerenciadas-pelo-cliente)
  - [Políticas Inline](#políticas-inline)
- [Recursos Seguros por Padrão](#recursos-seguros-por-padrão)
- [Melhores Práticas](#melhores-práticas)
- [Casos de Uso Comuns](#casos-de-uso-comuns)
- [Importância para Certificação AWS](#importância-para-certificação-aws)
- [Próximos Passos](#próximos-passos)
- [Links Rápidos do Console AWS](#links-rápidos-do-console-aws)

---

## O que é AWS IAM?

AWS IAM (Identity and Access Management) é um serviço da AWS que permite gerenciar de forma segura o acesso aos recursos e serviços da AWS. O IAM é fundamental para manter a segurança da sua infraestrutura na nuvem.

## Conceitos Principais

### 1. **Usuários (Users)**
Usuários representam pessoas ou aplicações que precisam acessar recursos AWS. Cada usuário tem:
- Um nome único
- Credenciais (senha e/ou chaves de acesso)
- Permissões definidas através de políticas

**📍 Console AWS:** [Gerenciar Usuários](https://console.aws.amazon.com/iam/home#/users)

### 2. **Grupos (Groups)**
Grupos são coleções de usuários. Você pode:
- Organizar usuários por função ou departamento
- Aplicar políticas a grupos em vez de usuários individuais
- Facilitar o gerenciamento de permissões em larga escala

**📍 Console AWS:** [Gerenciar Grupos](https://console.aws.amazon.com/iam/home#/groups)

### 3. **Roles (Funções)**
Roles são identidades que você cria com permissões específicas e que podem ser assumidas por:
- Usuários da sua conta AWS
- Serviços AWS (como Lambda, EC2)
- Identidades externas (usuários de outras contas AWS ou serviços federados)

**📍 Console AWS:** [Gerenciar Roles](https://console.aws.amazon.com/iam/home#/roles)

### 4. **Políticas (Policies)**
Políticas são documentos JSON que definem permissões. Elas especificam:
- **Effect**: Permitir (Allow) ou Negar (Deny)
- **Action**: Ações que podem ser executadas (ex: `s3:GetObject`, `ec2:StartInstances`)
- **Resource**: Recursos específicos onde a ação pode ser executada
- **Condition**: Condições opcionais para aplicar a política

**📍 Console AWS:** [Gerenciar Políticas](https://console.aws.amazon.com/iam/home#/policies)

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

**📍 Console AWS:** [Políticas Gerenciadas pela AWS](https://console.aws.amazon.com/iam/home#/policies?policyFilter=aws)

### **Políticas Gerenciadas pelo Cliente**
- Criadas e gerenciadas por você
- Permitem controle total sobre as permissões
- Reutilizáveis em múltiplos usuários/grupos/roles

**📍 Console AWS:** [Políticas Gerenciadas pelo Cliente](https://console.aws.amazon.com/iam/home#/policies?policyFilter=customer)

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
3. **Habilite CloudTrail** para auditoria de ações IAM - [Configurar CloudTrail](https://console.aws.amazon.com/cloudtrail/)
4. **Revise permissões regularmente** e remova acesso desnecessário - [Access Analyzer](https://console.aws.amazon.com/iam/home#/access-analyzer)
5. **Use grupos** para gerenciar permissões em vez de aplicar diretamente aos usuários
6. **Teste políticas** em ambiente de desenvolvimento antes de produção - [IAM Policy Simulator](https://console.aws.amazon.com/iam/home#/policies)
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

## Links Rápidos do Console AWS

### Navegação Principal
- **Dashboard IAM:** [Página Inicial do IAM](https://console.aws.amazon.com/iam/home)
- **Usuários:** [Gerenciar Usuários](https://console.aws.amazon.com/iam/home#/users)
- **Grupos:** [Gerenciar Grupos](https://console.aws.amazon.com/iam/home#/groups)
- **Roles:** [Gerenciar Roles](https://console.aws.amazon.com/iam/home#/roles)
- **Políticas:** [Gerenciar Políticas](https://console.aws.amazon.com/iam/home#/policies)

### Ferramentas e Recursos
- **Credential Report:** [Relatório de Credenciais](https://console.aws.amazon.com/iam/home#/users/report)
- **Access Analyzer:** [Analisador de Acesso](https://console.aws.amazon.com/iam/home#/access-analyzer)
- **Policy Simulator:** [Simulador de Políticas](https://policysim.aws.amazon.com/)
- **Account Settings:** [Configurações da Conta](https://console.aws.amazon.com/iam/home#/account_settings)
- **Security Credentials:** [Credenciais de Segurança](https://console.aws.amazon.com/iam/home#/security_credentials)

### Políticas
- **Políticas AWS Gerenciadas:** [Políticas da AWS](https://console.aws.amazon.com/iam/home#/policies?policyFilter=aws)
- **Políticas do Cliente:** [Minhas Políticas](https://console.aws.amazon.com/iam/home#/policies?policyFilter=customer)

### Segurança
- **MFA Devices:** [Dispositivos MFA](https://console.aws.amazon.com/iam/home#/users)
- **Password Policy:** [Política de Senhas](https://console.aws.amazon.com/iam/home#/account_settings/password_policy)
- **CloudTrail:** [CloudTrail - Auditoria](https://console.aws.amazon.com/cloudtrail/)

---

**Recursos Oficiais:**
- [Documentação AWS IAM](https://docs.aws.amazon.com/iam/)
- [AWS IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html)

