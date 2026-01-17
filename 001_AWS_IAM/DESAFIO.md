# Desafio: Implementando Segurança com AWS IAM

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Criar Grupos Organizacionais](#task-1-criar-grupos-organizacionais)
  - [Task 2: Criar Política Customizada para Desenvolvedores](#task-2-criar-política-customizada-para-desenvolvedores)
  - [Task 3: Criar Política Customizada para Analistas](#task-3-criar-política-customizada-para-analistas)
  - [Task 4: Anexar Políticas aos Grupos](#task-4-anexar-políticas-aos-grupos)
  - [Task 5: Criar Usuários e Adicionar aos Grupos](#task-5-criar-usuários-e-adicionar-aos-grupos)
  - [Task 6: Criar Role para Lambda](#task-6-criar-role-para-lambda)
  - [Task 7: Testar Permissões](#task-7-testar-permissões)
  - [Task 8: Implementar MFA (Opcional Avançado)](#task-8-implementar-mfa-opcional-avançado)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Criar uma estrutura IAM segura seguindo o princípio do menor privilégio, implementando usuários, grupos, políticas customizadas e roles para diferentes cenários de uso.

## Pré-requisitos

- Conta AWS ativa (preferencialmente conta de teste/sandbox)
- Acesso ao console AWS IAM
- Conhecimento básico de JSON para políticas
- AWS CLI instalado e configurado (opcional, para testes avançados)

## Cenário

Você foi contratado como DevOps Engineer para configurar o acesso IAM de uma startup. A equipe precisa de:

1. **Equipe de Desenvolvimento**: Precisa fazer deploy de aplicações Lambda e ler/escrever no S3
2. **Equipe de Análise**: Precisa apenas ler dados do S3 e visualizar logs no CloudWatch
3. **Serviço Lambda**: Precisa acessar um bucket S3 específico e escrever logs no CloudWatch

## Tasks

### Task 1: Criar Grupos Organizacionais

**Descrição:** Criar grupos para organizar usuários por função.

**Ações:**
1. Criar grupo `Developers`
2. Criar grupo `Analysts`

**Resultado Esperado:**
- Dois grupos criados no IAM
- Grupos sem políticas anexadas inicialmente

---

### Task 2: Criar Política Customizada para Desenvolvedores

**Descrição:** Criar uma política que permite aos desenvolvedores gerenciar Lambdas e acessar S3, seguindo o princípio do menor privilégio.

**Requisitos da Política:**
- Permitir criar, atualizar, deletar e invocar funções Lambda
- Permitir ler e escrever objetos em um bucket S3 específico (substituir `meu-bucket-dev`)
- Permitir listar buckets S3
- NEGAR todas as outras ações

**Ações:**
1. Criar política customizada chamada `DeveloperPolicy`
2. Use o editor JSON para criar a política

**Resultado Esperado:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "lambda:CreateFunction",
        "lambda:UpdateFunctionCode",
        "lambda:UpdateFunctionConfiguration",
        "lambda:DeleteFunction",
        "lambda:InvokeFunction",
        "lambda:ListFunctions"
      ],
      "Resource": "*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "s3:PutObject",
        "s3:GetObject",
        "s3:DeleteObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::meu-bucket-dev",
        "arn:aws:s3:::meu-bucket-dev/*"
      ]
    },
    {
      "Effect": "Deny",
      "Action": "*",
      "Resource": "*",
      "Condition": {
        "StringNotEquals": {
          "aws:RequestedRegion": "us-east-1"
        }
      }
    }
  ]
}
```

**Nota:** A política acima inclui uma condição opcional para restringir a uma região específica. Ajuste conforme necessário.

---

### Task 3: Criar Política Customizada para Analistas

**Descrição:** Criar uma política somente leitura para a equipe de análise.

**Requisitos da Política:**
- Permitir apenas leitura de objetos no S3
- Permitir apenas visualização de logs no CloudWatch (sem capacidade de criar ou deletar)
- Listar recursos, mas não modificá-los

**Ações:**
1. Criar política customizada chamada `AnalystReadOnlyPolicy`

**Resultado Esperado:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:ListBucket"
      ],
      "Resource": [
        "arn:aws:s3:::meu-bucket-dev",
        "arn:aws:s3:::meu-bucket-dev/*"
      ]
    },
    {
      "Effect": "Allow",
      "Action": [
        "cloudwatch:Describe*",
        "cloudwatch:Get*",
        "cloudwatch:List*"
      ],
      "Resource": "*"
    }
  ]
}
```

---

### Task 4: Anexar Políticas aos Grupos

**Descrição:** Conectar as políticas criadas aos grupos correspondentes.

**Ações:**
1. Anexar `DeveloperPolicy` ao grupo `Developers`
2. Anexar `AnalystReadOnlyPolicy` ao grupo `Analysts`

**Resultado Esperado:**
- Grupo `Developers` possui `DeveloperPolicy` anexada
- Grupo `Analysts` possui `AnalystReadOnlyPolicy` anexada

---

### Task 5: Criar Usuários e Adicionar aos Grupos

**Descrição:** Criar usuários e organizá-los nos grupos apropriados.

**Ações:**
1. Criar usuário `dev-john` e adicionar ao grupo `Developers`
2. Criar usuário `dev-maria` e adicionar ao grupo `Developers`
3. Criar usuário `analyst-pedro` e adicionar ao grupo `Analysts`
4. Para cada usuário, configurar senha inicial (obrigar mudança no próximo login)
5. Gerar chaves de acesso para `dev-john` (para uso com AWS CLI)

**Resultado Esperado:**
- Três usuários criados
- Usuários associados aos grupos corretos
- `dev-john` e `dev-maria` herdam permissões do grupo `Developers`
- `analyst-pedro` herda permissões do grupo `Analysts`
- Chaves de acesso criadas e seguras (Access Key ID e Secret Access Key)

---

### Task 6: Criar Role para Lambda

**Descrição:** Criar uma role que permite que funções Lambda acessem S3 e CloudWatch Logs.

**Requisitos:**
- Tipo de entidade confiável: AWS Service → Lambda
- Política de permissão: Acesso para ler/escrever em S3 e escrever logs no CloudWatch

**Ações:**
1. Criar role chamada `LambdaExecutionRole`
2. Selecionar serviço: Lambda
3. Anexar política `AmazonS3FullAccess` (temporariamente, depois vamos customizar)
4. Ou criar política customizada com apenas:
   - `s3:GetObject`, `s3:PutObject` no bucket específico
   - `logs:CreateLogGroup`, `logs:CreateLogStream`, `logs:PutLogEvents`

**Resultado Esperado:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "s3:GetObject",
        "s3:PutObject"
      ],
      "Resource": "arn:aws:s3:::meu-bucket-dev/*"
    },
    {
      "Effect": "Allow",
      "Action": [
        "logs:CreateLogGroup",
        "logs:CreateLogStream",
        "logs:PutLogEvents"
      ],
      "Resource": "arn:aws:logs:*:*:*"
    }
  ]
}
```

**Role criada com:**
- Trust policy configurada para o serviço Lambda
- Permissões apropriadas anexadas

---

### Task 7: Testar Permissões

**Descrição:** Validar que as permissões estão funcionando corretamente.

**Ações:**
1. Fazer login com `dev-john` no console AWS
2. Tentar criar uma função Lambda simples (deve funcionar)
3. Tentar criar um bucket S3 (deve falhar - não tem permissão)
4. Tentar acessar bucket `meu-bucket-dev` e fazer upload de arquivo (deve funcionar)
5. Fazer login com `analyst-pedro`
6. Tentar fazer upload no S3 (deve falhar - somente leitura)
7. Tentar visualizar logs no CloudWatch (deve funcionar)

**Resultado Esperado:**
- `dev-john` consegue criar Lambda e escrever no S3
- `dev-john` não consegue criar buckets
- `analyst-pedro` consegue ler do S3 e visualizar logs
- `analyst-pedro` não consegue escrever no S3

---

### Task 8: Implementar MFA (Opcional Avançado)

**Descrição:** Adicionar uma camada extra de segurança para conta administrativa.

**Ações:**
1. Criar um usuário `admin-lucas`
2. Anexar política `AdministratorAccess` (ou criar política customizada com permissões administrativas limitadas)
3. Configurar MFA para este usuário usando aplicativo autenticador (Google Authenticator, Authy, etc.)

**Resultado Esperado:**
- Usuário `admin-lucas` criado
- MFA habilitado e funcionando
- Login requer código MFA além da senha

---

## Resultados Esperados Finais

Ao final do desafio, você deve ter:

✅ **2 Grupos** criados e organizados
✅ **2 Políticas customizadas** seguindo o princípio do menor privilégio
✅ **3-4 Usuários** criados e organizados nos grupos corretos
✅ **1 Role** para Lambda com permissões específicas
✅ **Testes validados** confirmando que permissões funcionam como esperado
✅ Entendimento prático de como IAM funciona na AWS

## Checklist de Validação

- [ ] Grupos criados sem políticas inicialmente
- [ ] Políticas customizadas criadas (não usando apenas políticas AWS gerenciadas)
- [ ] Políticas seguem o princípio do menor privilégio
- [ ] Usuários não têm permissões inline, apenas através de grupos
- [ ] Role Lambda tem trust policy correta
- [ ] Testes confirmam que permissões estão corretas
- [ ] Documentação das políticas (comentários no código ou README)

## Dicas de Troubleshooting

1. **Permissão negada?** Verifique:
   - Política anexada corretamente?
   - Resource ARN está correto?
   - Condições na política não estão bloqueando?

2. **Testando com AWS CLI:**
   ```bash
   aws configure --profile dev-john
   # Insira Access Key e Secret Key
   aws lambda list-functions --profile dev-john
   ```

3. **Simulador de Políticas:**
   - Use o IAM Policy Simulator no console AWS para testar políticas antes de aplicá-las

4. **CloudTrail:**
   - Habilite CloudTrail para ver logs de ações e debugar problemas de permissão

## Extras (Opcional)

Se quiser ir além:

- Crie políticas com condições baseadas em IP
- Implemente políticas baseadas em tags de recursos
- Configure cross-account access usando roles
- Implemente rotação automática de chaves de acesso
- Crie script automatizado para provisionar essa estrutura (Terraform/CloudFormation)

## Próximos Passos

Após completar este desafio, você estará pronto para:
- Integrar IAM com outros serviços AWS (S3, Lambda, etc.)
- Implementar segurança em projetos reais
- Entender como permissões funcionam em diferentes serviços AWS
- Avançar para conceitos de S3 e Lambda no próximo módulo

---

**Tempo estimado:** 1-2 horas
**Dificuldade:** Intermediária
**Importância:** ⭐⭐⭐⭐⭐ (Fundamental para toda infraestrutura AWS)

