# Desafio: Implementando Armazenamento e Distribuição com S3 e CloudFront

## Índice

- [Objetivo](#objetivo)
- [Pré-requisitos](#pré-requisitos)
- [Cenário](#cenário)
- [Tasks](#tasks)
  - [Task 1: Criar Bucket S3](#task-1-criar-bucket-s3)
  - [Task 2: Configurar Bucket Policy](#task-2-configurar-bucket-policy)
  - [Task 3: Habilitar Versionamento](#task-3-habilitar-versionamento)
  - [Task 4: Criar Lifecycle Policy](#task-4-criar-lifecycle-policy)
  - [Task 5: Configurar Static Website Hosting](#task-5-configurar-static-website-hosting)
  - [Task 6: Criar Distribuição CloudFront](#task-6-criar-distribuição-cloudfront)
  - [Task 7: Configurar Origin Access Control](#task-7-configurar-origin-access-control)
  - [Task 8: Testar Distribuição CloudFront](#task-8-testar-distribuição-cloudfront)
- [Resultados Esperados Finais](#resultados-esperados-finais)
- [Checklist de Validação](#checklist-de-validação)
- [Dicas de Troubleshooting](#dicas-de-troubleshooting)
- [Extras (Opcional)](#extras-opcional)
- [Próximos Passos](#próximos-passos)

---

## Objetivo

Implementar uma solução completa de armazenamento e distribuição usando S3 e CloudFront, incluindo configurações de segurança, versionamento, lifecycle policies e CDN para distribuição global de conteúdo.

## Pré-requisitos

- Conta AWS ativa (preferencialmente conta de teste/sandbox)
- Acesso ao console AWS S3 e CloudFront
- Conhecimento básico de JSON para políticas
- AWS CLI instalado e configurado (opcional, para testes avançados)
- Conclusão do módulo AWS IAM (para usar usuários/permissões criados)

## Cenário

Você foi contratado como DevOps Engineer para configurar uma solução de armazenamento e distribuição de conteúdo para uma startup. A equipe precisa de:

1. **Bucket S3**: Para armazenar assets estáticos (imagens, CSS, JS) do site
2. **Versionamento**: Para manter histórico de arquivos e recuperação de deletados
3. **Lifecycle Policy**: Para otimizar custos movendo arquivos antigos para storage classes mais baratas
4. **Bucket Policy**: Para controlar acesso ao bucket
5. **CloudFront**: Para distribuir conteúdo globalmente com baixa latência
6. **Origin Access Control**: Para proteger o bucket S3, permitindo acesso apenas via CloudFront

## Tasks

### Task 1: Criar Bucket S3

**Descrição:** Criar um bucket S3 para armazenar assets estáticos do site.

**Requisitos:**
- Nome do bucket deve ser único globalmente (use seu nome + timestamp ou algo único)
- Escolha uma região próxima (ex: `us-east-1`, `sa-east-1`)
- Configurações iniciais:
  - **Block Public Access**: Manter habilitado inicialmente (vamos configurar depois)
  - **Versionamento**: Desabilitado inicialmente (vamos habilitar na Task 3)
  - **Default encryption**: Habilitar (SSE-S3 ou SSE-KMS)

**Ações:**
1. Acessar S3 Console
2. Criar bucket com nome único (ex: `meu-site-assets-2026`)
3. Escolher região
4. Habilitar default encryption
5. Manter Block Public Access habilitado

**Resultado Esperado:**
- Bucket criado e visível no console S3
- Encryption habilitada por padrão
- Block Public Access habilitado

---

### Task 2: Configurar Bucket Policy

**Descrição:** Criar uma bucket policy que permite acesso público apenas para leitura de objetos específicos.

**Requisitos da Política:**
- Permitir `s3:GetObject` para todos no bucket
- Permitir `s3:ListBucket` apenas para objetos específicos (opcional)
- Usar principal `*` ou `AWS: *` para acesso público
- Restringir a recursos específicos do bucket

**Ações:**
1. Acessar bucket criado
2. Ir em "Permissions" → "Bucket policy"
3. Criar política JSON

**Resultado Esperado:**
```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicReadGetObject",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::MEU-BUCKET/*"
    }
  ]
}
```

**Nota:** Substitua `MEU-BUCKET` pelo nome do seu bucket. Para aplicações reais, considere usar CloudFront + OAC ao invés de acesso público direto.

---

### Task 3: Habilitar Versionamento

**Descrição:** Habilitar versionamento no bucket para manter histórico de objetos.

**Requisitos:**
- Habilitar versionamento no bucket
- Testar upload de arquivo com mesmo nome
- Verificar versões no console

**Ações:**
1. Acessar bucket criado
2. Ir em "Properties" → "Bucket Versioning"
3. Habilitar versionamento
4. Fazer upload de um arquivo de teste (ex: `test.txt`)
5. Modificar o arquivo e fazer upload novamente com mesmo nome
6. Verificar versões na aba "Versions"

**Resultado Esperado:**
- Versionamento habilitado no bucket
- Múltiplas versões do mesmo objeto visíveis
- Versões anteriores preservadas após upload de nova versão

---

### Task 4: Criar Lifecycle Policy

**Descrição:** Criar uma lifecycle policy para otimizar custos movendo objetos antigos para storage classes mais baratas.

**Requisitos da Política:**
- Transicionar objetos para `STANDARD_IA` após 30 dias sem acesso
- Transicionar objetos para `GLACIER` após 90 dias
- Deletar versões antigas após 365 dias (opcional)

**Ações:**
1. Acessar bucket criado
2. Ir em "Management" → "Lifecycle rules"
3. Criar regra de lifecycle
4. Configurar transições de storage class

**Resultado Esperado:**
```json
{
  "Rules": [
    {
      "Id": "TransitionToIA",
      "Status": "Enabled",
      "Transitions": [
        {
          "Days": 30,
          "StorageClass": "STANDARD_IA"
        },
        {
          "Days": 90,
          "StorageClass": "GLACIER"
        }
      ]
    }
  ]
}
```

**Nota:** Ajuste os dias conforme necessário. Para buckets com versionamento, considere regras para versões antigas.

---

### Task 5: Configurar Static Website Hosting

**Descrição:** Configurar o bucket S3 para hospedar um site estático.

**Requisitos:**
- Habilitar Static Website Hosting
- Definir index document (ex: `index.html`)
- Definir error document (ex: `error.html`)
- Anotar o Endpoint URL do site

**Ações:**
1. Acessar bucket criado
2. Ir em "Properties" → "Static website hosting"
3. Habilitar hosting estático
4. Definir `index.html` como index document
5. Definir `error.html` como error document (opcional)
6. Fazer upload de um arquivo `index.html` simples de teste

**Resultado Esperado:**
- Static website hosting habilitado
- Endpoint URL disponível (formato: `http://BUCKET-NAME.s3-website-REGION.amazonaws.com`)
- Arquivo `index.html` acessível via endpoint (após configurar bucket policy)

**Exemplo de `index.html`:**
```html
<!DOCTYPE html>
<html>
<head>
    <title>Meu Site S3</title>
</head>
<body>
    <h1>Bem-vindo ao meu site hospedado no S3!</h1>
    <p>Este é um teste de static website hosting.</p>
</body>
</html>
```

---

### Task 6: Criar Distribuição CloudFront

**Descrição:** Criar uma distribuição CloudFront para distribuir conteúdo do bucket S3 globalmente.

**Requisitos:**
- Origin: Bucket S3 criado
- Viewer Protocol Policy: Redirect HTTP to HTTPS
- Allowed HTTP Methods: GET, HEAD, OPTIONS
- Cache Policy: CachingOptimized (ou customizada)
- Default Root Object: `index.html`

**Ações:**
1. Acessar CloudFront Console
2. Criar distribuição
3. Selecionar bucket S3 como origin
4. Configurar Viewer Protocol Policy: Redirect HTTP to HTTPS
5. Definir Default Root Object: `index.html`
6. Criar distribuição (pode levar 10-15 minutos para deployment)

**Resultado Esperado:**
- Distribuição CloudFront criada
- Status: "Deployed"
- Domain Name CloudFront disponível (formato: `d1234567890.cloudfront.net`)
- Distribuição apontando para o bucket S3

**Configurações Importantes:**
- **Origin**: Selecionar bucket S3
- **Origin Access**: Manter "Legacy access identities" inicialmente (vamos mudar na Task 7)
- **Viewer Protocol Policy**: Redirect HTTP to HTTPS
- **Allowed HTTP Methods**: GET, HEAD, OPTIONS
- **Cache Policy**: CachingOptimized
- **Default Root Object**: `index.html`

---

### Task 7: Configurar Origin Access Control

**Descrição:** Configurar Origin Access Control (OAC) para proteger o bucket S3, permitindo acesso apenas via CloudFront.

**Requisitos:**
- Criar Origin Access Control no CloudFront
- Atualizar distribuição para usar OAC
- Atualizar bucket policy para permitir acesso apenas via CloudFront
- Desabilitar acesso público direto ao S3

**Ações:**
1. Criar OAC no CloudFront Console
   - Settings → Origins → Edit Origin
   - Origin Access → Origin Access Control Settings
   - Create Control Setting
   - Nome: `S3BucketOAC`
   - Signing behavior: Simple signing
2. Atualizar bucket policy para permitir acesso apenas via CloudFront:
   ```json
   {
     "Version": "2012-10-17",
     "Statement": [
       {
         "Sid": "AllowCloudFrontServicePrincipal",
         "Effect": "Allow",
         "Principal": {
           "Service": "cloudfront.amazonaws.com"
         },
         "Action": "s3:GetObject",
         "Resource": "arn:aws:s3:::MEU-BUCKET/*",
         "Condition": {
           "StringEquals": {
             "AWS:SourceArn": "arn:aws:cloudfront::ACCOUNT-ID:distribution/DISTRIBUTION-ID"
           }
         }
       }
     ]
   }
   ```
3. Remover a bucket policy de acesso público (criada na Task 2)
4. Verificar que Block Public Access está habilitado

**Resultado Esperado:**
- OAC criado e anexado à distribuição CloudFront
- Bucket policy permite acesso apenas via CloudFront
- Acesso direto ao S3 bloqueado
- Acesso via CloudFront funcionando

**Nota:** Obtenha o Distribution ID e Account ID da distribuição CloudFront criada na Task 6.

---

### Task 8: Testar Distribuição CloudFront

**Descrição:** Validar que a distribuição CloudFront está funcionando corretamente.

**Testes a Realizar:**

1. **Teste de Acesso via CloudFront:**
   - Acessar URL do CloudFront: `https://d1234567890.cloudfront.net`
   - Deve carregar o `index.html`
   - Verificar HTTPS funcionando

2. **Teste de Acesso Direto ao S3 (deve falhar):**
   - Tentar acessar bucket S3 diretamente via URL pública
   - Deve retornar erro 403 (Access Denied) ou não funcionar

3. **Teste de Cache:**
   - Acessar URL CloudFront múltiplas vezes
   - Verificar headers de cache na resposta (X-Cache: Hit from cloudfront)

4. **Teste de Upload de Novos Arquivos:**
   - Fazer upload de um novo arquivo no bucket S3
   - Acessar via CloudFront
   - Verificar que arquivo aparece (pode levar alguns minutos devido ao cache)

5. **Teste de Invalidação (Opcional):**
   - Criar invalidação no CloudFront para um arquivo específico
   - Acessar arquivo via CloudFront
   - Verificar que nova versão é servida

**Resultado Esperado:**
- ✅ Acesso via CloudFront funciona (HTTPS)
- ✅ Acesso direto ao S3 bloqueado
- ✅ Cache funcionando
- ✅ Novos arquivos aparecem via CloudFront
- ✅ Site estático acessível globalmente

**Comandos AWS CLI (Opcional):**
```bash
# Testar acesso via CloudFront
curl -I https://d1234567890.cloudfront.net

# Ver headers de cache
curl -v https://d1234567890.cloudfront.net

# Criar invalidação
aws cloudfront create-invalidation \
  --distribution-id DISTRIBUTION-ID \
  --paths "/*"
```

---

## Resultados Esperados Finais

Ao final do desafio, você deve ter:

✅ **1 Bucket S3** criado e configurado  
✅ **Versionamento** habilitado  
✅ **Lifecycle Policy** configurada  
✅ **Static Website Hosting** habilitado  
✅ **Bucket Policy** configurada (acesso via CloudFront)  
✅ **1 Distribuição CloudFront** criada e deployed  
✅ **Origin Access Control** configurado  
✅ **Testes validados** confirmando que distribuição funciona  
✅ Entendimento prático de como S3 e CloudFront funcionam juntos  

## Checklist de Validação

- [ ] Bucket S3 criado com encryption habilitada
- [ ] Bucket policy configurada (acesso via CloudFront)
- [ ] Versionamento habilitado e funcionando
- [ ] Lifecycle policy criada e ativa
- [ ] Static website hosting habilitado
- [ ] Distribuição CloudFront criada e deployed
- [ ] Origin Access Control configurado
- [ ] Acesso direto ao S3 bloqueado
- [ ] Acesso via CloudFront funcionando (HTTPS)
- [ ] Cache funcionando corretamente
- [ ] Testes validados confirmando que configuração está correta

## Dicas de Troubleshooting

1. **Bucket não acessível via CloudFront?** Verifique:
   - OAC configurado corretamente?
   - Bucket policy permite CloudFront?
   - Distribution ID correto na bucket policy?

2. **CloudFront ainda mostrando conteúdo antigo?**
   - Aguarde alguns minutos (propagação pode levar tempo)
   - Crie invalidação para forçar atualização

3. **Erro 403 ao acessar CloudFront?**
   - Verifique Default Root Object está configurado
   - Verifique que `index.html` existe no bucket
   - Verifique bucket policy permite CloudFront

4. **Testando com AWS CLI:**
   ```bash
   # Verificar bucket policy
   aws s3api get-bucket-policy --bucket MEU-BUCKET
   
   # Verificar versionamento
   aws s3api get-bucket-versioning --bucket MEU-BUCKET
   
   # Listar versões de um objeto
   aws s3api list-object-versions --bucket MEU-BUCKET --prefix test.txt
   ```

5. **CloudFront Status:**
   - Status "Deployed" significa que está pronto
   - Status "In Progress" significa que ainda está sendo configurado
   - Aguarde até aparecer "Deployed" antes de testar

## Extras (Opcional)

Se quiser ir além:

- Configure custom domain (ex: `cdn.seudominio.com`)
- Use AWS Certificate Manager (ACM) para certificado SSL customizado
- Configure WAF no CloudFront para proteção adicional
- Implemente Signed URLs/Cookies para conteúdo privado
- Configure geo-restriction no CloudFront
- Crie múltiplas origins no CloudFront
- Configure cache behaviors customizados por path
- Implemente CloudFront Functions para manipular requests/responses

## Próximos Passos

Após completar este desafio, você estará pronto para:

- Integrar S3 e CloudFront com aplicações Lambda
- Implementar sites estáticos completos na AWS
- Otimizar custos com lifecycle policies
- Configurar CDN para APIs e aplicações web
- Avançar para conceitos de Lambda no próximo módulo

---

**Tempo estimado:** 2-3 horas  
**Dificuldade:** Intermediária  
**Importância:** ⭐⭐⭐⭐⭐ (Fundamental para armazenamento e distribuição na AWS)
